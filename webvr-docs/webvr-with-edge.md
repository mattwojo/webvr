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

## You can do a lot with WebVR.
- Create 3D virtual objects.
- Create immersive 3D virtual worlds.
- Display 360º panoramic images.
- Engage users with 3D interfaces and game controllers.

## Goals of the WebVR API*
- Detect available Virtual Reality devices.
- Query the devices capabilities.
- Poll the device’s position and orientation.
- Display imagery on the device at the appropriate frame rate.

**According to the [W3C working group](https://github.com/w3c/webvr/blob/master/explainer.md).*

## Test WebVR support with your headset

The CodePen embedded below should enable you to test support for your VR headset with the Microsoft Edge. Just click the headset icon below.

<iframe height='265' scrolling='no' title='WebVR experiment' src='//codepen.io/mattwojo/embed/preview/Evqjpx/?height=265&theme-id=0&default-tab=html,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/mattwojo/pen/Evqjpx/'>WebVR test</a> (<a href='https://codepen.io/mattwojo'></a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>


## WebVR and WebGL
If you are already using WebGL to render interactive 3D graphics in a browser, the switch to WebVR will be a natural continuation of your work. We will have more documentation and interactive demos utilizing WebVR and WebGL coming soon.

## Related videos
MR motion controllers video: https://www.youtube.com/watch?v=1nlcdDNOdm8
Mixed Reality blends physical and virtual spaces (what is mixed reality?): https://youtu.be/_xpI0JosYUk 