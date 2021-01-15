# WebVR Developers Guide
TOC:
- User Comfort
- Input
- WebVR Essentials

Potential Additions:
- Toolset
- Javascript Gotcha's

# User Comfort
The human body is very sensitive to various cues, including visual, auditory, touch etc. It uses all of these in combination to maintain a sense of orientation. If any of these senses reports something that the brain doesn't expect, the user will rapidly experience discomfort or nausea. A real world parallel is motion sickness, which in some cases can be completely dabilitating.

Everybody is different - something that feels fine for you may cause crippling nausea for other people. A common experience in VR apps that can instantly cause nausea is in some people but not others is translation: using a thumbpad or WASD to 'walk'. Affected people will suffer within seconds - it is not only prolonged exposure that you need to worry about.

There is comprehensive must-read [comfort documentation](https://developer.microsoft.com/en-us/windows/mixed-reality/comfort) on the Windows Dev Center, however some of the important takeaways are summarized below:

## Assume all headsets are 6DOF
Only some of the headsets on the market right now have sensors capable of reporting full [6DOF](https://en.wikipedia.org/wiki/Six_degrees_of_freedom) motion, some have sensors that will only report head rotation. Sensor limitation does not prevent the device from making a guess about its location in space via neck modelling techniques, which users find [uncomfortable if omitted](https://www.reddit.com/r/oculus/comments/3uhs63/why_dont_all_gear_vr_apps_have_the_neck_model_its/).

Omitting head position support will cause discomfort for users, especially when objects are rendered close to their head. By supporting the full six degrees of motion in your app, not only do you increase comfort for 3DOF headset users, you will get the benefit of full room scale support for free with devices that support it.

360 video presents an interested exception to this case, since the camera itself was fixed in space when the video was recorded. The best practice here is to simply make the sphere/cube on which you project the video very large in space (say, 100x100m) so that any in-world UI elements you may add will respect head movement.

## Constant, high frame rate
> __LW: Not happy with this section__

One thing that is especially jarring for users and can cause extreme discomfort is a sudden pause in rendering. It is critical that the application draws to the headset at the hardware defined screen refresh rate. While a constant low frame rate is typically caused by expensive code in the `requestAnimationFrame` callback or GPU wait, a sudden stop in rendering is typically caused by blocking code in `requestAnimationFrame`, or a failure to queue another callback. 

Extra caution will need to be taken if you perform loading or creation of assets within the render loop, while rendering to the headset. It is preferable to show a simple, dark loading screen consideration in the headset while expensive main thread operations take place.

## Focal Zone
Placement of objects in the world with respect to the viewers focal point is also important for comfort. 

To view objects clearly, humans must [accommodate](https://en.wikipedia.org/wiki/Accommodation_%28eye%29), or adjust their eyes’ focus, to the distance of the object. At the same time, the two eyes must [converge](https://en.wikipedia.org/wiki/Convergence_(eye)) to the object’s distance to avoid seeing double images. In natural viewing, vergence and accommodation are linked. When you view something near (e.g. a housefly close to your nose) your eyes cross and accommodate to a near point. Conversely, if you view something at infinity, your eyes’ lines of sight become parallel and the your eyes accommodates to infinity. In most head-mounted displays users will always accommodate to the focal distance of the display (to get a sharp image), but converge to the distance of the object of interest (to get a single image). When users accommodate and converge to different distances, the natural link between the two cues must be broken and this can lead to visual discomfort or fatigue.

![Focal Plane](img/DistanceGuideRendering.png)

### Best practices

The precise value for the focal distance varies per device as it depends on the setup of the display. In general, focal distance to displays are between 1.25m-2m. 

- Try and keep most content at least 1m away from the display - use a pixel shader start fading out at that distance, with a clipping plane at 0.85m. 
- Avoid putting content further away than 5m - user fatigue increases the further away objects are outside of the focal range.
- Objects that move in depth (especially close to the user) are likely to increase user fatigue.
- If a user spends >25% of the time with objects closer <1m away, or moving in depth, we recommend careful user testing to ensure it remains comfortable.

### Examples

__Movie screens__, or other items that you expect the user to look at for extend periods, should be as close to the focal plane as possible (1.25m-2m away from the user). If in doubt, provide the user a method to move closer or farther from a point of interest.

__User interface__ elements in-world should be on the focal plane so as to maximise user comfort (don't place them closer than 1.25m).

__Tracked controller__ 3D representations will often be closer to the user than the focal guidance above. Use best judgement - use the 3D controller models to assist with user awareness of where their hands are, rather than as an information display/HUD. The general guidance is that the application should maximise comfort for users - if neccisary fade the controllers to transparent if they are held closer than a 'cross eyes' distance or too close to the face. 

# Input
Input is one of the toughest things to get right in VR, but is essential to creating a compelling interactive experience. Input is used to convert a cinematic experience into an compelling interactive one by allowing a user to alter the state of the world around them. Interaction could be as simple as playing/pausing a video by gazing at a button, to a complex drag action requiring two tracked controllers and multiple user steps.

Right now in WebVR 1.1, a site should reasonably expect visitors to limited to using only one of the following input methods (their device or situation may prohibit use of any others)

- Mouse
- Keyboard
- Gamepad
  - Traditional (Xbox Controller)
  - 3DOF (Pointing & buttons)
  - 6DOF (Pointing, Position and buttons)

I describe the best way to handle each in individual sections below, followed by a comprehensive example showing all input working together to provide a robust multi-platform input handler.

## Mouse
Since a person wearing an HMD can’t actually see what they are doing with an unrestricted mouse, it would be very easy for them to accidently click on things they don’t intend to – the browser close button, other windows or the operating system interface. In a worst case this could potentially lead to things such as an accidental format of a hard drive! For this reason, Microsoft Edge disables unrestricted mouse input when an immersive VR session is started and the headset presence sensor is covered (you’ll see the Win+Alt+I banner visible on your monitor when this is the case). This is the behavior for all apps in Windows Mixed Reality and is not unique to WebVR.

The safest way to handle mouse input when in an immersive WebVR session is to use the [pointerlock API](https://msdn.microsoft.com/en-us/library/mt560346.aspx). There are two events on the WebVR spec that are specifically designed to make this easy – they are fired at the appropriate times when the page enters VR, HMD presence sensor detects changes and any number of other reasons input focus would change: [vrdisplaypointerrestricted](/previous-versions//mt801974(v=vs.85)) and [vrdisplaypointerunrestricted](/previous-versions//mt801975(v=vs.85)) (also see the WebVR 1.1 spec [event descriptions](https://w3c.github.io/webvr/spec/1.1/#window-onvrdisplaypointerrestricted-event)). In response to the former you can request pointerlock to get mouse input.

To remain compatible with browsers that do not fire those events, it is still a good idea to also request pointerlock just prior to calling requestPresent for the user safety reasons mentioned above. 

The below snippet shows an example of safely using pointerlock to handle mouse down events.  [View a full working sample at codepen.io](https://codepen.io/ransico/pen/dRXxVY?editors=0010)

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

## Keyboard
TODO: This section.

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

__Known controllers and their mappings:__
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
If you are using three.js, see the [ray-input](https://www.npmjs.com/package/ray-input) module for a good gaze-and-commit implementation that listens across multiple input sources and supports progressive enhancement as more capable input sources are detected.

The following snippet shows one way to listen for a primary action across multiple input sources. This could be incorporated into a wider gaze-and-commit system. Full working source code is available: [WebVR Aggregated Primary Input](https://codepen.io/ransico/pen/PjmRGJ)

```javascript
function init() {
  
  // Defines what happens when user input is detected.
  myAction = new Action(
    function(source, active) { 
      // This is executed on any mouse button down, gamepad button down, or keyboard 'space' key down. 
      setState(active ? source : null); 
    });  
  
  // Register for mouse restriction events, so we can request pointerlock.
  // See https://codepen.io/ransico/pen/dRXxVY for more details on this pointerlock implementation
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