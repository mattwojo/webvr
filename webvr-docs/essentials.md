---
title: WebVR Essentials
description: WebVR essentials include inclusive features and capability detection, automatically entering VR on page load, and plugging in HMD.
author: leweaver
ms.author: leweaver
ms.date: 08/01/2017
ms.topic: article
ms.prod: webvr
keywords: WebVR essentials, Inclusive Features, Capability Detection, page load, plugging in HMD
---


# WebVR Functionality Checklist
This article outlines some essential best practices to ensure that your WebVR experiance works great across a range of browsers and hardware. It starts with a checklist that outlines some common traps, and how to avoid them. Later, we present some general best practices and sample code that will help, even if you are using a WebGL library (such as [BabylonJS](https://www.babylonjs.com/), [a-frame](https://aframe.io/), [React VR](https://facebook.github.io/react-vr/), [threejs](https://threejs.org/)) to create your experience.

The following checklist is split into four categories. Meeting all points in this list will ensure you have a robust WebVR experience in Microsoft Edge and other browsers. The __Foundations__ and __Hybrid__ sections are essential for all WebVR content; __Mouse Input__ and __Controller Input__ sections apply if your experience utilizes those input sources.

## Foundations
- Applications should gracefully handle a null value for VRDisplay.stageParameters (Microsoft Edge does not support stage parameters at this time.)
- Assume that `navigator.getVRDisplays` is always present in the browser; you must make a call to that function to determine if a VRDisplay is actually connected. The getVRDisplays promise will reject on systems that do not natively support MR.
-	Users may plug in their headset after the page has loaded, or disconnect & reconnect without reloading the page. Handle this through the `vrdisplayconnect` and `vrdisplaydisconnect` event.

## Hybrid
The 1.1 specification was recently amended to add support for multi-GPU systems, such as hybrid laptops with an integrated and more powerful GPU. In order for these machines to correctly support WebVR, they must either:
- [c]orrectly handle](https://www.khronos.org/webgl/wiki/HandlingContextLost) the webglcontextlost and webglcontextrestored events.
- if the page does not handle webglcontextrestored correctly, ensure that handlers to webglcontextlost do NOT call arg0.preventDefault(), as that will opt-out of our fallback behavior.

## Mouse Input
Some platforms (including Windows Mixed Reality) disable the mouse cursor on the 2D desktop when the user is wearing a headset. Mouse input (position deltas and button presses) can be accessed whilst in an exclusive mode WebVR application through the use of pointer lock, which should be requested on a page element (usually the canvas, for convenience). Note that whilst the page is presenting to an HMD and mouse input is restricted, the requirement for user consent to get pointerlock is waived. 

- To enable mouse clicks whilst in VR, pages should request pointerlock in response to the [vrdisplaypointerrestricted](https://w3c.github.io/webvr/spec/1.1/#window-onvrdisplaypointerrestricted-event) event.

## Controller Input
Windows Motion Controllers are only present in the array returned by a call to navigator.getGamepads() while the page is presenting to the headset, and will always be in array positions 4 or greater. The array itself will have a dynamic length, ranging from 4 to 6 (or more) elements. Once presentation stops, the controllers are removed from the array. Gaps in the array will be filled with null.

Care should be taken to:
- Iterate over the entire length of the gamepads array, and make no assumptions on controllers being at given array indices. 
- Check that array entries are valid using `!!`, rather than `!== null`.
- Not assume controllers are accessible in the array prior to presentation starting.
- Handle cases where one or more controllers are disconnected or (re)connected whilst presenting.
- When examining the gamepad id (to detect which glTF/OBJ model to render, or which button mappings to use) use `gamepad.id.indexOf("Spatial Controller (Spatial Interaction Source)") !== -1` rather than an equality operator (`===`) since the end of the id string is subject to change.
- Handle a "primary action" on a press of button index 1 as well as 0

Full mappings of Windows Motion Controllers exposed via the gamepad API:
<table>
  <tr><th>Button/Axis Index</th><th>Mapping</th></tr>
  <tr><td>buttons[0]</td><td>Thumbstick</td></tr>
  <tr><td>buttons[1]</td><td>Select/Trigger</td></tr>
  <tr><td>buttons[2]</td><td>Grasp</td></tr>
  <tr><td>buttons[3]</td><td>Menu</td></tr>
  <tr><td>buttons[4]</td><td>Touchpad</td></tr>
  <tr><td>axes[0]</td><td>Thumbstick X</td></tr>
  <tr><td>axes[1]</td><td>Thumbstick Y</td></tr>
  <tr><td>axes[2]</td><td>Touchpad X</td></tr>
  <tr><td>axes[3]</td><td>Touchpad Y</td></tr>
</table>

# Inclusive Feature and Capability Detection
When determining whether or not to enable your WebVR feature (as opposed to a fallback 2D screen rendered version), do so based on device capability rather than device name. This approach means compatible devices that reach the market after you code your site will "just work". Don't exclude things just because you haven't tested it yet. In general, avoid making functional decisions based on meta data, such as the `VRDisplay.displayName`, as this will prevent your site working on future hardware.

Just because the WebVR API is present in the browser doesn’t mean an HMD is plugged in. Browsers are in the process of rolling out the WebVR 1.1 standard in their stable branches right now, so assume that in the very near future, all users could potentially have the API present in their browser whether or not they have a headset or even know what VR is!

Once you determine that the WebVR API exists, make a call to `navigator.getVRDisplays()`. The promise returned by this method will only resolve with an entry if a headset is plugged in - which is always true on a mobile but not always the case on desktop. If a VRDisplay is returned, examine the various attributes to determine the capability and state of the returned device such as `VRDisplay.hasExternalDisplay`. This promise may be rejected if the system is not capable of supporting WebVR (eg, if no drivers are installed)

# Sample Code
## The Enter VR button
The Enter VR button is a visual cue that your content can present to an external headset, so should be visible on systems that support the API, even no headset is plugged in. _Feature detection_ can be used to determine the visibility (and enabled state) of the button.

The button should conform to the general visual conventions that have been adopted by many sites: an simple stencil image of some VR goggles, the words "Enter VR" are also common. The button itself should be accessible by keyboard tabbing and visually respond to being in focus.

Typical Enter VR button image:
![Enter VR button from Mozillas' A-Frame](img/enter-vr.png)

It is helpful to bind a keyboard button, such as 'E', to the control.

The button should have one of 3 possible states: 
- __enabled__: Visible and white. Clicking will make a call to requestPresent.
- __disabled__: Visible but greyed. Clicking will perform a no-op, or display a message directing the user to plug in a headset.
- __hidden__: Invisible.

The logic to determine which of these three states the button is in is summarized below, with some reference javascript code: 
- If `navigator.getVRDisplays` method DOESN'T EXIST, hide the button.
- If `navigator.getVRDisplays` method EXISTS, show the button
  - If promise fulfils with 0 displays, disable the button.
  - If promise fulfils with 1 (or more) VRDisplays, enable the button.
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

## Users may plug in their HMD after loading your website. 
If you are performing any logic whatsoever on page load that makes decisions based on the presence of a VRDisplay, such as enabling an Enter VR button, make sure you listen for the `window.onvrdisplayconnect` (and associated `window.onvrdisplaydisconnect`) events.

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

# Advanced Features

## Automatically entering VR when the page loads
__Important:__ not all platforms support this. Always provide an Enter VR button as well.

In browsers that do not require the page to call `requestPresent` from within the context of a user initiated action, it is possible to make the call at any time. Thus, to attempt to enter VR immedately, simply place a call to `requestPresent` after initialization completes.

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
