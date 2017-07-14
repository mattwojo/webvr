---
title: WebVR Essentials
description: WebVR essentials include inclusive features and capability detection, automatically entering VR on page load, and plugging in HMD.
author: <!-- GitHub alias -->
ms.author: leweaver
ms.date: 08/01/2017
ms.topic: article
ms.prod: webvr
keywords: WebVR essentials, Inclusive Features, Capability Detection, page load, plugging in HMD
---

# WebVR Essentials

## Inclusive Feature and Capability Detection
When determining whether or not to enable your WebVR feature (as opposed to a fallback 2D screen rendered version), do so based on device capability rather than device name. This approach means compatible devices that reach the market after you code your site will "just work". Don't exclude things just because you haven't tested it yet.

Just because the WebVR API is present in the browser doesn’t mean an HMD is plugged in. Browsers are in the process of rolling out the WebVR 1.1 standard in their stable branches right now, so assume that in the very near future, all users could potentially have the API present in their browser whether or not they have a headset or even know what VR is!

Once you determine that the WebVR API exists, make a call to `navigator.getVRDisplays()`. The method will only return an entry if a headset is plugged in - which is always true on a mobile but not always the case on desktop. If a VRDisplay is returned, examine the various attributes to determine the capability and state of the returned device such as `VRDisplay.hasExternalDisplay`.

Don't make functional decisions based on meta data, such as the `VRDisplay.displayName`, as this will prevent your site working on future hardware.

This is a great segue into...

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
  - If calling the method returns 0 displays, disable the button.
  - If calling the method returns 1 (or more) VRDisplays, enable the button.

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