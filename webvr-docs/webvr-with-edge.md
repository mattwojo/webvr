---
title: WebVR with Microsoft Edge
description: Introduction to using WebVR with the Microsoft Edge browser.
author: mattwojo
ms.author: mattwoj
ms.date: 05/17/2018
ms.topic: article
ms.prod: microsoft-edge
ms.technology: webvr
keywords: WebVR, Microsoft Edge
---

# Using WebVR with Microsoft Edge

The Microsoft Edge browser (build 15002+ with the Windows 10 Creators Update or later) supports immersive 3D Virtual Reality (VR) applications on the web using the WebVR 1.1 JavaScript API. Viewing WebVR content requires a [Windows Mixed Reality headset](hardware.md), or the [Windows Mixed Reality Portal Simulator](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-mixed-reality-simulator) (accessible via Developer Mode).

> [!Note]
> To check which version of Microsoft Edge you currently have installed:
> 1. Open Microsoft Edge.
> 2. In the top-right corner of the browser, select … to open the menu.
> 3. Select **Settings**.
> 4. Scroll down to find your version number under the **About this app** heading.
>
> Updates to Edge are automatically installed when Windows 10 is updated. To keep Edge up to date, you need to keep Windows 10 up to date. To see which version of Windows 10 your device is currently running, select the **Start** button, then **Settings** > **System** > **About**.
>
> To get Microsoft Edge build 15002+, you must be running the Windows 10 Creators Update or later. Go to the [Microsoft software download website](https://www.microsoft.com/software-download/windows10), and select **Update now** to install the latest version of Windows 10. While technically support for WebVR was added to Edge in the Creators Update, we highly recommend that you run at least the Fall Creators Update, as there were many bug fixes and important changes added.
>
> The WebVR API surface area is present at all times within Microsoft Edge. However, a call to [getVRDisplays](https://developer.mozilla.org/docs/Web/API/Navigator/getVRDisplays) will only return a headset if the operating system has been placed in Developer Mode, and either a headset is plugged in or the simulator has been turned on.

## What's new in the Windows 10 April 2018 Update

With the Windows 10 April 2018 Update, you can now run WebVR inside JavaScript/HTML Windows 10 applications, including PWAs (Progressive Web Apps). See [WebVR in Progressive Web Apps](webvr-in-pwas.md) for more information.

Additionally, you can now run WebVR inside WebView controls in Windows 10 apps. See [Microsoft Edge WebView for Windows 10 apps](https://docs.microsoft.com/microsoft-edge/webview) for more information about WebView.

## You can do a lot with WebVR on Microsoft Edge

- Create 3D virtual objects.
- Create immersive 3D virtual worlds.
- Display 360º panoramic images.
- Engage users with 3D interfaces and game controllers.
- Play [Babylon.js](https://www.babylonjs.com/) 3D games right in your browser.

<!-- **[Test it below](#test-webvr-support-with-your-headset) or [see some demos](demos.md).* -->

## Setting up your Mixed Reality headset

If you have a Windows Mixed Reality headset, follow the [Immersive headset setup guide](https://docs.microsoft.com/windows/mixed-reality/enthusiast-guide/before-you-start) to get started. If you do not have a physical device, you can instead use the [Windows Mixed Reality simulator](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-mixed-reality-simulator) to develop and test your WebVR experience.

- Ensure you have a 64-bit version of Windows 10&mdash;[Check your compatibility](https://docs.microsoft.com/windows/mixed-reality/enthusiast-guide/windows-mixed-reality-minimum-pc-hardware-compatibility-guidelines)
- Ensure your Windows 10 installation is [up-to-date](https://support.microsoft.com/help/4028685/windows-10-get-the-update)
- [How to launch the Mixed Reality Portal](https://docs.microsoft.com/windows/mixed-reality/enthusiast-guide/install-windows-mixed-reality)
- [Connect your Headset](https://docs.microsoft.com/windows/mixed-reality/enthusiast-guide/plug-in-your-headset) or [turn on Simulation](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-mixed-reality-simulator)

### Additional resources

- [Where can I buy a Windows Mixed Reality headset? + other FAQs](https://docs.microsoft.com/windows/mixed-reality/enthusiast-guide/before-you-buy-faqs)
- [How to set up Motion Controllers, Room Boundary, Speech](https://docs.microsoft.com/windows/mixed-reality/enthusiast-guide/set-up-windows-mixed-reality)
- [Troubleshooting](https://docs.microsoft.com/windows/mixed-reality/enthusiast-guide/troubleshooting-windows-mixed-reality)

## Running WebVR on your Mixed Reality headset

To experience WebVR content on a Windows Mixed Reality headset (using hardware or simulation) you must, with your headset connected:

1. Launch Microsoft Edge either on the desktop, or within Mixed Reality.
2. Navigate to a WebVR-enabled page.
3. Click the **Enter VR** button within the page (the location and visual representation of this button may vary per website).
4. The first time you try to enter VR on a specific domain, the browser will ask for consent to use immersive view. Click **Yes**.
5. Your headset will begin presenting.

**Press the Windows button or escape key to exit the immersive view.*

## Test WebVR support with your headset

The code samples below enable you to test support for your VR headset with Microsoft Edge. Just click the headset icon to enter WebVR mode.

Rendering and animating a simple shape using [Babylon.js](//www.babylonjs.com/):

<iframe height='300' scrolling='no' title='WebVR sample in Microsoft Edge with BabylonJS' src='//codepen.io/MicrosoftEdgeDocumentation/embed/QqrXLM/?height=300&theme-id=31247&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/QqrXLM/'>WebVR sample in Microsoft Edge with BabylonJS</a> by Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

Rendering and animating shapes using [A-Frame](//aframe.io):

<iframe height='300' scrolling='no' title='WebVR sample in Micrsoft Edge with A-frame' src='//codepen.io/MicrosoftEdgeDocumentation/embed/RLwjYL/?height=300&theme-id=31247&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/RLwjYL/'>WebVR sample in Micrsoft Edge with A-frame</a> by Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

Viewing a 360-degree photograph using [A-frame](//aframe.io):

<iframe height='300' scrolling='no' title='WebVR 360-degree image with Microsoft Edge' src='//codepen.io/MicrosoftEdgeDocumentation/embed/MEgBJd/?height=300&theme-id=31247&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/MEgBJd/'>WebVR 360-degree image with Microsoft Edge</a> by Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## Add WebVR support to your 3D Babylon.js game in the Microsoft Edge browser

If you've created a 3D game with Babylon.js and thought that it might look great in easily-accessible virtual reality on the web, follow the steps in this tutorial to [add WebVR support to your Babylon.js game](//docs.microsoft.com/windows/uwp/get-started/adding-webvr-to-a-babylonjs-game).