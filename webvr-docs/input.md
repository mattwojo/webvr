---
title: Input
description: Input is used to convert a cinematic experience into an compelling interactive one by allowing a user to alter the state of the world around them. 
author: leweaver
ms.author: leweaver
ms.date: 08/01/2017
ms.topic: article
ms.prod: webvr
keywords: WebVR Input
---

# Input
Input is one of the toughest things to get right in VR, but is essential to creating a compelling interactive experience. Input is used to convert a cinematic experience into an interactive one by allowing a user to alter the state of the world around them. Interaction could be as simple as playing/pausing a video by gazing at a button, to a complex drag action requiring two tracked controllers.

In WebVR 1.1, a site will most likely expect visitors to be limited to using only one of the following input methods due to their device.

- Mouse
- Gamepad
  - Traditional (Xbox Controller)
  - 3DOF (Pointing & buttons)
  - 6DOF (Pointing, Position and buttons)

Let's dig in to how to handle each input device and see how all these devices can be gathered from at once, providing a robust, multi-platform input handler.


## Mouse
Since a person wearing an HMD can’t actually see what they are doing with an unrestricted mouse, it would be very easy for them to accidently click on things they don’t intend to – the browser close button, other windows or the operating system interface. In a worst case this could potentially lead to things such as an accidental format of a hard drive! For this reason, Microsoft Edge disables unrestricted mouse input when an immersive VR session is started and the headset presence sensor is covered (you’ll see the Win+Alt+I banner visible on your monitor when this is the case). This is the behavior for all apps in Windows Mixed Reality and is not unique to WebVR.

The safest way to handle mouse input when in an immersive WebVR session is to use the [pointerlock API](https://msdn.microsoft.com/en-us/library/mt560346.aspx). There are two events on the WebVR spec that are specifically designed to make this easy – they are fired at the appropriate times when the page enters VR, HMD presence sensor detects changes and any number of other reasons input focus would change: [vrdisplaypointerrestricted](https://msdn.microsoft.com/en-us/library/mt801974) and [vrdisplaypointerunrestricted](https://msdn.microsoft.com/en-us/library/mt801975) (also see the WebVR 1.1 spec [event descriptions](https://w3c.github.io/webvr/spec/1.1/#window-onvrdisplaypointerrestricted-event)). In response to the former you can request pointerlock to get mouse input.

To remain compatible with browsers that do not fire those events, it is still a good idea to also request pointerlock just prior to calling requestPresent for the user safety reasons mentioned above. 


The only way to get mouse input while presenting to a VRDisplay is to request pointerlock. This should be done at two places: 
- Just before making a call to VRDisplay.requestPresent
- In response to a vrdisplaypointerrestricted event, which is called whenever mouse input context changes from HMD to Monitor (such as uncovering the presence sensor on a Windows Mixed Reality headset)



The below snippet shows an example of safely using pointerlock to handle mouse down events. 

<iframe height='279' scrolling='no' title='Pointerlock webvr' src='//codepen.io/MicrosoftEdgeDocumentation/embed/preview/xXKagP/?height=279&theme-id=31247&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/xXKagP/'>Pointerlock webvr</a> by Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


```javascript
// Globals that you would need to initialize properly in a real app - out of scope of this example.
let webglCanvas = null, vrDisplay = null;

/* Called once after page load. You would also initialize your WebGL context and resources here as well as . */
function initialize() {
  // Subscribe to event handlers as you require (handle clicks for experience logic, etc)
  webglCanvas.addEventListener('mousedown', (e) => console.log('Mouse Down'));

  // Register for the pointer restriction events, for browsers that disable unrestricted mouse when a headset has taken input
  window.addEventListener('vrdisplaypointerrestricted', () => webglCanvas.requestPointerLock(), false);
  window.addEventListener('vrdisplaypointerunrestricted', document.exitPointerLock(), false);

  // Fallback for browsers that do not fire unrestricted events: exit pointer lock when we stop presenting, as it is safe to use the mouse again.
  window.addEventListener('vrdisplaypresentchange', () => !vrDisplay.isPresenting && document.exitPointerLock(), false);
}

/* This method would be invoked when the user clicks the Enter VR button */
function tryRequestPresent() {
    
    // Request pointerlock manually for browsers that do not raise vrpointerrestricted events.
    // This method is not foolproof, since browsers reserve the right to eject the user from pointlock at any time.
    if (typeof(window.onvrdisplaypointerrestricted) === 'undefined')
      webglCanvas.requestPointerLock();

    vrDisplay.requestPresent([{source: webglCanvas}])
      .then(
        () => console.log('In VR'),
        () => console.error('Failed to enter VR')
      );
}
```

## Gamepad
Traditional, 0DOF, 3DOF & 6DOF controllers are all exposed via the navigator.getGamepads() array. Since the specification does not state anything about a minimum or maximum length for the array, nor the order in which elements appear, it is safest to assume all browser vendors implement it differently (and guess what, they do!).

It is especially important when dealing with gamepads to make no assumptions whatsoever on the type, ordering or number of devices that will be returned returned in the array. As an example, Windows Mixed Reality users may be using [motion controllers](https://developer.microsoft.com/en-us/windows/mixed-reality/motion_controllers) or a gamepad to navigate the browser in VR; they will expect those controllers to continue working once they enter a WebVR immersive session. 

You experience can easily determine the capability of all of the plugged in devices using the [pose](https://w3c.github.io/gamepad/extensions.html#partial-gamepad-interface) property on the gamepad. It is bad practice to to look at the `gamepad.id` property for this purpose.

```javascript
if (gamepad.mapping === 'standard')
  'Standard Gamepad';
else if (gamepad.pose && gamepad.pose.hasOrientation && gamepad.pose.hasPosition)
  '6DOF: Pointing and position';
else if (gamepad.pose && gamepad.pose.hasOrientation)
  '3DOF: Pointing only';
else
  '0DOF Clicker, or other';
```

### Controller buttons
_TL;DR:_ 
* There is a lot of variation between hardware
* Supporting both [point-and-commit and gaze-and-commit](https://developer.microsoft.com/en-us/windows/mixed-reality/gestures#gaze-and-commit]) gives users choice of input device
* Commit if gamepad buttons 0 or 1 are pressed _(check that the buttons exist)_
* Relying on controllers with lots of buttons reduces your target audience, unless you provide an alternative input method

Traditional gamepad button mappings are [well documented](https://www.w3.org/TR/gamepad/#remapping) in the W3C specification. You can be sure that a gamepad is using that configuration only by reading the [Gamepad.mapping](https://www.w3.org/TR/gamepad/#dfn-mapping) property:

```javascript
if (gamepad.mapping === 'standard') console.log(gamepad.id + ' is a traditional gamepad');
```

Things get interesting with all of the VR Controllers since each one has different hardware configuration: number of buttons, thumbstick, touchpad etc. While there are no mappings defined in the specification for any of the VR controllers, we can base our approach on the knowledge that the [specification states](https://www.w3.org/TR/gamepad/#dfn-buttons): 

>  It is recommended that buttons appear in decreasing importance such that the primary button, secondary button, tertiary button, and so on appear as elements 0, 1, 2, ... in the buttons array

It is convention that the trigger on many 6DOF controllers is the primary, "most important" button, however controllers in WebVR 1.1 is always place the trigger at index 1 if it is present. Controllers that lack a trigger correctly place the "most important" button at index 0.

There is no way in the current WebVR 1.1 API to reliably determine whether or not a controller has trigger. For gaze-and-commit or point-and-commit, it is recommended to listen for a primary (select) action on both button index 0 and 1. (_be careful with the button array size, the controller may not have a button defined at index 1_)

Known controllers and their mappings:
<!-- This should be converted into a markdown table -->
<table>
  <tr><th></th><th>Windows Mixed Reality </th><th>Oculus Touch</th><th>Vive controller</th><th>GearVR controller</th><th>Oculus Remote</th><th>Daydream Controller</th><th>GearVR headset</th></tr>
  <tr><th>  </th><th>6DOF</th><th>6DOF</th><th>6DOF</th><th>3DOF</th><th>3DOF</th><th>6DOF</th><th>Head Mounted</th></tr>
  <tr><th>buttons[0]</th><td>Thumbstick</td><td>Thumbstick</td><td>Touchpad</td><td><strong>?</strong></td><td>Inner Ring <strong>^</strong></td><td>Trackpad <strong>^</strong></td><td> </td></tr>
  <tr><th>buttons[1]</th><td>Select <strong>^</strong></td><td>Trigger <strong>^</strong></td><td>Trigger <strong>^</strong></td><td><strong>?</strong></td><td>Back</td><td>Menu <strong>#</strong></td><td> </td></tr>
  <tr><th>buttons[2]</th><td>Grasp</td><td>Grip</td><td>Grips</td><td> </td><td>Outer Ring - Up</td><td>System <strong>#</strong></td><td> </td></tr>
  <tr><th>buttons[3]</th><td>Menu</td><td>A/X</td><td>Menu</td><td> </td><td>Outer Ring - Down</td><td> </td><td> </td></tr>
  <tr><th>buttons[4]</th><td>Touchpad</td><td>B/Y</td><td> </td><td> </td><td>Outer Ring - Left</td><td> </td><td> </td></tr>
  <tr><th>buttons[5]</th><td> </td><td>Surface</td><td> </td><td> </td><td>Outer Ring - Right</td><td> </td><td> </td></tr>
  <tr><th>Buttons[6]</th><td> </td><td>Menu<strong>?</strong></td><td> </td><td> </td><td> </td><td> </td><td> </td></tr>
  <tr><th>axes[0]</th><td>Thumbstick X</td><td>Thumbstick X</td><td>Touchpad X</td><td><strong>?</strong></td><td> </td><td>Trackpad X</td><td> </td></tr>
  <tr><th>axes[1]</th><td>Thumbstick Y</td><td>Thumbstick Y</td><td>Touchpad Y</td><td> </td><td> </td><td>Trackpad Y</td><td> </td></tr>
  <tr><th>axes[2]</th><td>Touchpad X</td><td> </td><td> </td><td> </td><td> </td><td> </td><td> </td></tr>
  <tr><th>axes[3]</th><td>Touchpad Y</td><td> </td><td> </td><td> </td><td> </td><td> </td><td> </td></tr>
  <tr><th>Touch “click”</th><td> </td><td> </td><td> </td><td><strong>?</strong></td><td> </td><td>Click (to be removed.)</td><td>Tap</td></tr>
  <tr><th>Touch “scroll”</th><td> </td><td> </td><td> </td><td><strong>?</strong></td><td> </td><td> </td><td>Drag</td></tr>
</table>

* __^__ _Primary Button_
* __#__ _Libraries on github have mapped this, but it doesn’t exist on the Gamepad Buttons array in Chrome on mobile._
* __?__ _Needs verification from the implementers group._

So in practice, the easiest way for your experience to support all gamepads is to use a simple gaze-and-commit interaction style, listening for button presses in either index 0 OR 1 for the commit. Remember to be careful with array size - the controller may not actually have more than 1 button!

### Rendering controller models
The only time it is really useful to use the `gamepad.id` property is to render a model of a your controller. You could, for example, use the string returned by that field to look up a glTF file. If you take this approach, remember to include a generic default or fallback model in the event that a controller with an unforeseen id appears.

## Putting it all together

The following CodePen shows one way to listen for a primary action across multiple input sources. This could be incorporated into a wider gaze-and-commit system. 


The canvas will pulse a different colour depending on what input source the action came from:
Grey: Deactivated: No action detected
Red: Pointer (Mouse Click or Touchpad on the canvas element)
Blue: Keyboard Space Bar
Green: Gamepad

<iframe height='300' scrolling='no' title='Multi input detection' src='//codepen.io/MicrosoftEdgeDocumentation/embed/preview/oGvPpM/?height=300&theme-id=31247&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/oGvPpM/'>Multi input detection</a> by Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


If we take a close look at the JavaScript behind the example,
```javascript
function init() {
  
  // Defines what happens when user input is detected.
  myAction = new Action(
    function(source, active) { 
      // This is executed on any mouse button down, gamepad button down, or keyboard 'space' key down. 
      setState(active ? source : null); 
    });  
  
  // Register for mouse restriction events, so we can request pointerlock.
  // See https://codepen.io/MicrosoftEdgeDocumentation/pen/oGvPpM for more details on this pointerlock implementation
  window.addEventListener('vrdisplaypointerrestricted', () => webglCanvas.requestPointerLock(), false);
  window.addEventListener('vrdisplaypointerunrestricted', () => document.exitPointerLock(), false);  
  window.addEventListener('vrdisplaypresentchange', () => { if (!vrDisplay.isPresenting) document.exitPointerLock() }, false);
  
  // Register for mouse (any button) event handlers
  webglCanvas.addEventListener('mousedown', () => myAction.activate('mouse'));
  webglCanvas.addEventListener('mouseup', () => myAction.deactivate('mouse'));
  // Register for keyboard (space bar) 
  window.addEventListener("keydown", (event) => { if (event.key === ' ') { myAction.activate('keyboard'); event.preventDefault(); }});
  window.addEventListener("keyup",   (event) => { if (event.key === ' ') { myAction.deactivate('keyboard'); event.preventDefault(); }});
}

// Simple helper function; simply checks if either button at index 0 or 1 is pressed on the given gamepad.
function isSelectButtonPressed(gamepad) {
  if (!gamepad.buttons || !gamepad.buttons.length) return false;
  if (gamepad.buttons[0].pressed) return true;
  if (gamepad.buttons.length > 1 && gamepad.buttons[1].pressed) return true;
  return false;
}

// This method is called one per frame, we need to read gamepad state here since the Gamepad API relies on Polling.
function tick() {
  // You should make a new call to getGamepads each frame. Here we use feature detection to gracefully handle absence of gamepad support.
  var gamepads = navigator.getGamepads ? navigator.getGamepads() : [];
  var activate = false;
  for (var i = 0; i < gamepads.length; i++) {
    if (gamepads[i] && isSelectButtonPressed(gamepads[i])) {
      activate = true;
      break;
    }
  }

  if (activate) myAction.activate('gamepad'); 
  else myAction.deactivate('gamepad');
}
```
