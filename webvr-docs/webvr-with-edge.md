---
title: WebVR with Microsoft Edge
description: Introduction to using WebVR with the Microsoft Edge browser.
author: mattwojo
ms.author: mattwoj
ms.date: 09/11/2017
ms.topic: article
ms.prod: microsoft-edge
ms.technology: webvr
keywords: WebVR, Microsoft Edge
---

# Using WebVR with Microsoft Edge

The Microsoft Edge browser (build 15002+ with Windows 10 Creators Update) now supports immersive 3D Virtual Reality (VR) applications on the web using the WebVR 1.1 JavaScript API. Viewing WebVR content requires a [Windows Mixed Reality headset](hardware.md), or the [Windows Mixed Reality Portal Simulator](//developer.microsoft.com/en-us/windows/mixed-reality/using_the_windows_mixed_reality_simulator) (accessible via Developer Mode in the Windows 10 Creators Update).

> [!Note]
> To check which version of Microsoft Edge you currently have installed, open Microsoft Edge, in the top-right corner of the browser select … to open the menu, then select "Settings", and scroll down to find your version number under the "About this app" heading. Updates to Edge are automatically installed when Windows 10 is updated. To keep Edge up to date, you need to keep Windows 10 up to date. To see which version of Windows 10 your device is currently running, select the Start  button, then Settings > System > About. To get Microsoft Edge build 15002+, you must be running Windows 10 Creators Update, go to the [Microsoft software download website](https://www.microsoft.com/software-download/windows10), and select Update now to install it. 
> The WebVR API surface area is present at all times within Microsoft Edge. However, a call to getVRDisplays() will only return a headset if the operating system has been placed in Developer Mode, and either a headset is plugged in or the simulator has been turned on.

## You can do a lot with WebVR on Microsoft Edge.
- Create 3D virtual objects.
- Create immersive 3D virtual worlds.
- Display 360º panoramic images.
- Engage users with 3D interfaces and game controllers.
- Play Babylon.js 3D games right in your browser.

<!-- **[Test it below](#test-webvr-support-with-your-headset) or [see some demos](demos.md).* -->

## Setting up your Mixed Reality headset

If you have a Windows Mixed Reality headset, follow the [Immersive headset setup guide](//developer.microsoft.com/en-us/windows/mixed-reality/immersive_headset_setup) to get started. If you do not have a physical device, you can instead use the [Windows Mixed Reality simulator](//developer.microsoft.com/en-us/windows/mixed-reality/using_the_windows_mixed_reality_simulator) to develop and test your WebVR experience.

- Ensure you have a 64-bit version of Windows 10 -- [Check your compatibility](//developer.microsoft.com/en-us/windows/mixed-reality/check_your_compatibility)
- Ensure you are running the [Windows 10 Fall Creators Update](//support.microsoft.com/en-us/help/4028685/windows-10-get-the-fall-creators-update) 
- [How to launch the Mixed Reality Portal](//developer.microsoft.com/en-us/windows/mixed-reality/install_windows_mixed_reality)
- [Connect your Headset](//developer.microsoft.com/en-us/windows/mixed-reality/plug_in_your_headset) or [turn on Simulation](//developer.microsoft.com/en-us/windows/mixed-reality/using_the_windows_mixed_reality_simulator)

### Additional resources: 
- [Where can I buy a Windows Mixed Reality headset? + other FAQs](//developer.microsoft.com/en-us/windows/mixed-reality/before_you_buy_-_faqs#when_can_i_buy_a_windows_mixed_reality_headset.3F)
- [How to set up Motion Controllers, Room Boundary, Speech](//developer.microsoft.com/en-us/windows/mixed-reality/set_up_windows_mixed_reality#set_up_your_motion_controllers)
-  [Troubleshooting](//developer.microsoft.com/en-us/windows/mixed-reality/troubleshooting_windows_mixed_reality)

## Running WebVR on your Mixed Reality headset

To experience WebVR content on a Windows Mixed Reality headset (using hardware or simulation) you must, with your headset connected:
- Launch Microsoft Edge either on the desktop, or within Mixed Realty
- Navigate to a WebVR enabled page
- Click the **Enter VR** button within the page (the location and visual representation of this button may vary per website)
- The first time you try to enter VR on a specific domain, the browser will ask for consent to use immersive view. Click **Yes**
- Your headset should begin presenting

**Press the Windows button or escape key to exit the immersive view.*

## Test WebVR support with your headset

The code samples below (embedded using [CodePen](//codepen.io/)) should enable you to test support for your VR headset with the Microsoft Edge. Just click the headset icon to enter WebVR mode.

A simple WebVR sample built using [Babylon.js](//www.babylonjs.com/): 
<iframe height='300' scrolling='no' title='WebVR sample in Microsoft Edge with BabylonJS' src='//codepen.io/MicrosoftEdgeDocumentation/embed/QqrXLM/?height=300&theme-id=31247&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/QqrXLM/'>WebVR sample in Microsoft Edge with BabylonJS</a> by Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

A simple WebVR sample built using [A-Frame](//aframe.io): 
<iframe height='300' scrolling='no' title='WebVR sample in Micrsoft Edge with A-frame' src='//codepen.io/MicrosoftEdgeDocumentation/embed/RLwjYL/?height=300&theme-id=31247&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/RLwjYL/'>WebVR sample in Micrsoft Edge with A-frame</a> by Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

A simple WebVR sample for viewing a 360-degree photograph -- this photo is from the middle of Greenlake in Seattle, WA (coded using [A-frame](//aframe.io)):

<iframe height='300' scrolling='no' title='WebVR 360-degree image with Microsoft Edge' src='//codepen.io/MicrosoftEdgeDocumentation/embed/MEgBJd/?height=300&theme-id=31247&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/MEgBJd/'>WebVR 360-degree image with Microsoft Edge</a> by Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## Play your 3D Babylon.js game in the Microsoft Edge browser by adding WebVR support

If you've created a 3D game with Babylon.js and thought that it might look great in easily-accessible virtual reality (VR) on the web, follow the steps in this tutorial to [add WebVR support to your Babylon.js game](//docs.microsoft.com/windows/uwp/get-started/adding-webvr-to-a-babylonjs-game).

