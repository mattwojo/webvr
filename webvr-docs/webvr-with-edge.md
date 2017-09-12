---
title: WebVR with Microsoft Edge
description: Introduction to using WebVR with the Microsoft Edge browser.
author: mattwojo
ms.author: mattwoj
ms.date: 09/11/2017
ms.topic: article
ms.prod: webvr
keywords: WebVR, Microsoft Edge
---

# Using WebVR with Microsoft Edge

The Microsoft Edge browser (build 15002+) now supports immersive 3D Virtual Reality (VR) applications on the web using the WebVR 1.1 JavaScript API. Viewing WebVR content requires a [Windows Mixed Reality headset](/headsets), or the [Windows Mixed Reality Portal Simulator](//developer.microsoft.com/en-us/windows/mixed-reality/using_the_windows_mixed_reality_simulator) (accessible via Developer Mode in the Windows 10 Creators Update).

> [!Note]
> To check which version of Microsoft Edge you currently have installed, open Microsoft Edge, in the top-right corner of the browser select … to open the menu, then select "Settings", and scroll down to find your version number under the "About this app" heading. Updates to Edge are automatically installed when Windows 10 is updated. To keep Edge up to date, you need to keep Windows 10 up to date. To see which version of Windows 10 your device is currently running, select the Start  button, then Settings > System > About. To get Microsoft Edge build 15002+, you must be running Windows 10 Creators Update, go to the [Microsoft software download website](https://www.microsoft.com/software-download/windows10), and select Update now to install it. 

## You can do a lot with WebVR on Microsoft Edge.
- Create 3D virtual objects.
- Create immersive 3D virtual worlds.
- Display 360º panoramic images.
- Engage users with 3D interfaces and game controllers.
- Play Babylon.js 3D games right in your browser.

**[Test it below](#test-webvr-support-with-your-headset) or [see some demos](demos.md).*

## Test WebVR support with your headset

The CodePen embedded below should enable you to test support for your VR headset with the Microsoft Edge. Just click the headset icon below.

<iframe height='300' scrolling='no' title='WebVR with MicrosoftEdge' src='//codepen.io/MicrosoftEdgeDocumentation/embed/PJYBNO/?height=300&theme-id=31247&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/PJYBNO/'>WebVR with MicrosoftEdge</a> by Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

Or try viewing this 360-degree photograph from the middle of Greenlake in Seattle, WA.

<iframe height='300' scrolling='no' title='WebVR 360-degree image with Microsoft Edge' src='//codepen.io/MicrosoftEdgeDocumentation/embed/MEgBJd/?height=300&theme-id=31247&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/MicrosoftEdgeDocumentation/pen/MEgBJd/'>WebVR 360-degree image with Microsoft Edge</a> by Microsoft Edge Docs (<a href='https://codepen.io/MicrosoftEdgeDocumentation'>@MicrosoftEdgeDocumentation</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

We used [A-Frame](https://aframe.io), a web framework for building VR experiences, to set up these simple VR tests and embedded them using [CodePen](https://codepen.io/).

## Play your 3D Babylon.js game in the Microsoft Edge browser by adding WebVR support

If you've created a 3D game with Babylon.js and thought that it might look great in easily-accessible virtual reality (VR) on the web, follow the steps in this tutorial to [add WebVR support to your Babylon.js game](//docs.microsoft.com/windows/uwp/get-started/adding-webvr-to-a-babylonjs-game).

