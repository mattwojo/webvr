---
title: WebVR functionality checklist
description: WebVR functionality checklist including features and capability detection, automatically entering VR on page load, and plugging in headset.
author: leweaver
ms.author: leweaver
ms.date: 07/11/2018
ms.topic: article
ms.prod: microsoft-edge
ms.technology: webvr
keywords: WebVR essentials, Inclusive Features, Capability Detection, page load, plugging in headset
---

# WebVR functionality checklist
This article outlines some good practices to ensure that your WebVR experience works great across a range of browsers and hardware. It starts with a checklist that outlines some common traps, and how to avoid them. Later, we present some general good practices and sample code that will help, even if you are using a WebGL library (such as [BabylonJS](https://www.babylonjs.com/), [a-frame](https://aframe.io/), [React VR](https://facebook.github.io/react-vr/), [threejs](https://threejs.org/)) to create your experience.

The following checklist is split into four categories. Meeting all points in this list will ensure you have a robust WebVR experience in Microsoft Edge and other browsers. The [Foundations](#foundations) and [Multi-GPU systems](#multi-gpu-systems) sections are essential for all WebVR content; [Mouse input](#mouse-input) and [Controller input](#controller-input) sections apply if your experience utilizes those input sources.

## Foundations
- Applications should gracefully handle a null value for [VRDisplay.stageParameters](https://developer.mozilla.org/docs/Web/API/VRDisplay/stageParameters).
- Assume that `navigator.getVRDisplays` is always present in the browser; you must make a call to that function to determine if a VRDisplay is actually connected. The getVRDisplays promise will reject on systems that do not natively support MR.
-	Users may plug in their headset after the page has loaded, or disconnect and reconnect without reloading the page. Handle this through the [vrdisplayconnect](https://developer.mozilla.org/en-US/docs/Web/Events/vrdisplayconnect) and [vrdisplaydisconnect](https://developer.mozilla.org/en-US/docs/Web/Events/vrdisplaydisconnect) events.

## Multi-GPU systems
The [WebVR 1.1](https://w3c.github.io/webvr/spec/1.1/) specification was recently amended to add support for multi-GPU systems, such as hybrid laptops with an integrated and more powerful GPU. For these machines to correctly support WebVR, they must either:
- [Correctly handle](https://www.khronos.org/webgl/wiki/HandlingContextLost) the webglcontextlost and webglcontextrestored events.
- If the page does not handle [webglcontextrestored](https://developer.mozilla.org/en-US/docs/Web/Events/webglcontextrestored) correctly, ensure that handlers to [webglcontextlost](https://developer.mozilla.org/en-US/docs/Web/Events/webglcontextlost) do NOT call arg0.preventDefault(), as that will opt-out of our fallback behavior.

## Mouse input
Some platforms (including Windows Mixed Reality) disable the mouse cursor on the 2D desktop when the user is wearing a headset. Mouse input (position deltas and button presses) can be accessed whilst in an exclusive mode WebVR application through the use of pointer lock, which should be requested on a page element (usually the canvas, for convenience). Note that whilst the page is presenting to an headset and mouse input is restricted, the requirement for user consent to get pointerlock is waived. 

- To enable mouse clicks whilst in VR, pages should request pointerlock in response to the [vrdisplaypointerrestricted](https://w3c.github.io/webvr/spec/1.1/#window-onvrdisplaypointerrestricted-event) event.

## Controller input
Windows Motion Controllers are only present in the array returned by a call to [navigator.getGamepads](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/getGamepads) while the page is presenting to the headset, and will always be in array positions 4 or greater. The array itself will have a dynamic length, ranging from 4 to 6 (or more) elements. Once presentation stops, the controllers are removed from the array. Gaps in the array will be filled with null.

Care should be taken to:
- Iterate over the entire length of the gamepads array, and make no assumptions on controllers being at given array indices. 
- Check that array entries are valid using `!!`, rather than `!== null`.
- Not assume controllers are accessible in the array prior to presentation starting.
- Handle cases where one or more controllers are disconnected or (re)connected whilst presenting.
- When examining the gamepad id (to detect which glTF/OBJ model to render, or which button mappings to use) use `gamepad.id.indexOf("Spatial Controller (Spatial Interaction Source)") !== -1` rather than an equality operator (`===`) since the end of the id string is subject to change.
- Handle a "primary action" on a press of button index 1 as well as 0

Full mappings of Windows Motion Controllers exposed via the gamepad API:

| Button/Axis Index | Mapping |
| ------ | ------ |
| buttons[0] | Thumbstick |
| buttons[1] | Select/Trigger |
| buttons[2] | Grasp | 
| buttons[3] | Menu | 
| buttons[4] | Touchpad | 
| axes[0] | Thumbstick X | 
| axes[1] | Thumbstick Y | 
| axes[2] | Touchpad X | 
| axes[3] | Touchpad Y | 


# Inclusive Feature and Capability Detection
When determining whether or not to enable your WebVR feature (as opposed to a fallback 2D screen rendered version), do so based on device capability rather than device name. This approach means compatible devices that reach the market after you code your site will "just work". Don't exclude things just because you haven't tested it yet. In general, avoid making functional decisions based on meta data, such as the [VRDisplay.displayName](https://developer.mozilla.org/en-US/docs/Web/API/VRDisplay/displayName), as this will prevent your site working on future hardware.

Just because the WebVR API is present in the browser doesn’t mean an headset is plugged in. Browsers are in the process of rolling out the WebVR 1.1 standard in their stable branches right now, so assume that in the very near future, all users could potentially have the API present in their browser whether or not they have a headset or even know what VR is!

Once you determine that the WebVR API exists, make a call to [navigator.getVRDisplays](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/getVRDisplays). The promise returned by this method will only resolve with an entry if a headset is plugged in - which is always true on a mobile but not always the case on desktop. If a VRDisplay is returned, examine the various attributes to determine the capability and state of the returned device such as [VRDisplay.hasExternalDisplay](https://developer.mozilla.org/en-US/docs/Web/API/VRDisplayCapabilities/hasExternalDisplay). This promise will be rejected if the system is not capable of supporting WebVR (eg, if no drivers are installed, or the system hardware cannot support VR). You can handle a rejected promise by falling back to a magic window experience (using device orientation API's) if appropriate.

# Sample code
## The Enter VR button
The Enter VR button is a visual cue that your content can present to an external headset, so should be visible on systems that support the API, even no headset is plugged in. Feature detection can be used to determine the visibility (and enabled state) of the button.

The button should conform to the general visual conventions that have been adopted by many sites: a simple stencil image of some VR goggles, the words "Enter VR" are also common. The button itself should be accessible by keyboard tabbing and visually respond to being in focus. The Babylon.js library uses the following image.


![Enter VR button from BabylonJS](img/enter-vr.png)

It is helpful to bind a keyboard button, such as 'E', to the control.

The button should have one of 3 possible states: 
- **enabled** -  Visible and white. Clicking will make a call to requestPresent.
- **disabled** - Visible but greyed. Clicking will perform a no-op, or display a message directing the user to plug in a headset.
- **hidden** - Invisible.

The logic to determine which of these three states the button is in is summarized below, with some reference javascript code: 
- If [navigator.getVRDisplays](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/getVRDisplays) method DOESN'T EXIST, hide the button.
- If `navigator.getVRDisplays` method EXISTS, show the button
  - If promise fulfils with 0 displays, disable the button.
  - If promise fulfils with 1 (or more) [VRDisplays](https://developer.mozilla.org/en-US/docs/Web/API/VRDisplay), enable the button.
  - If promise rejects, hide the button.

```javascript
// Returns a promise that will resolve with the name of the state in which to put the button. One of: hidden, disabled, enabled
function calculateButtonState() {
  if (navigator.getVRDisplays) {
    return navigator.getVRDisplays().then((displays) => {
      // This browser supports WebVR.
      return displays.length ? 'enabled' : 'disabled';
    }, () => {
      // This browser supports WebVR, but the API is disabled or inoperable.
      return 'hidden';
    });
  } else {
    // This browser does not support WebVR at all.
    return Promise.resolve('hidden');
  }
}
```

## Users may plug in their headset after loading your website. 
If you are performing any logic on page load that makes decisions based on the presence of a [VRDisplay](https://developer.mozilla.org/en-US/docs/Web/API/VRDisplay), such as enabling an Enter VR button, make sure you listen for the [window.onvrdisplayconnect](https://developer.mozilla.org/en-US/docs/Web/API/Window/onvrdisplayconnect) (and associated [window.onvrdisplaydisconnect](https://developer.mozilla.org/en-US/docs/Web/API/Window/onvrdisplaydisconnect)) events.

The below example shows one possible usage of the `vrdisplayconnect` event, to enable the Enter VR button when a headset is connected after page load.

```javascript
let vrDisplay = null;
function onPageLoadEventHandler() {

  // Get initial VRDipslay value\
  if (navigator.getVRDisplays) {
    navigator.getVRDisplays()
      .then((displays) => {
        if (displays.length)
          vrDisplay = displays[0];
      });
  }
  
  // Set the initial button state (uses the setEnterVRButtonState method defined in a previous example)
  calculateButtonState().then(setEnterVRButtonState);

  // Subscribe to the events to make sure new displays are detected
  window.addEventListener('vrdisplayconnect', (event) => { 
    vrDisplay = event.display; 
    setEnterVRButtonState('enabled');
  }, false );
  window.addEventListener('vrdisplaydisconnect', () => {
    vrdisplay = null;
    setEnterVRButtonState('disabled');
  }, false);
}
function setEnterVRButtonState(state) {
  // Apply CSS classes, show/hide help text, etc.
}
```

# Advanced features

## Automatically entering VR when the page loads
> [!IMPORTANT]
> Not all platforms support this. Always provide an Enter VR button as well.

In browsers that do not require the page to call [requestPresent](https://developer.mozilla.org/en-US/docs/Web/API/VRDisplay/requestPresent) from within the context of a user initiated action, it's possible to make the call at any time. To attempt to enter VR immedately, place a call to `requestPresent` after initialization completes.

```javascript
// On page load, simply make a call to your standard enterVR method
function onPageLoadEventHandler() {
  initWebGL();
  enterVR();
}
// Remember to provide a button fallback
function onEnterVRButtonPress() {
  enterVR();
}
```
