---
title: What is WebVR?
description: Introduction to WebVR.
author: eliotcowley
ms.author: elcowle
ms.date: 06/19/2018
ms.topic: article
ms.prod: microsoft-edge
ms.technology: webvr
keywords: WebVR, Microsoft Edge
---

# What is WebVR?

WebVR is an open API that brings the magic of virtual reality to internet browsers. It works across several different browsers and devices, so you can write your code once and run it cross-platform. The [official site](https://webvr.info/) has information about which browsers and devices are supported.

> [!NOTE]
> The WebVR specification is being replaced with the WebXR Device API specification. While you can continue to use the WebVR 1.1 APIs, it is a good idea to begin reviewing the WebXR specification on [their GitHub page](https://github.com/immersive-web/webxr).

## Why WebVR?

Today, there are many different VR devices on the market, with varying levels of compatibility, often with their own storefronts. This has led to a fragmentation of the VR ecosystem, where you can only access certain apps and games on certain platforms.

With WebVR, however, you can experience virtual reality in a platform- and device-agnostic way. This way, when you're developing a VR experience, you no longer have to choose which devices you're going to target, and develop them as separate experiences. You simply write it once using WebVR APIs, and you're automatically cross-platform.

## How do I develop WebVR experiences?

To get started developing with WebVR, you'll need a few things first:

* A compatible immersive headset
* A compatible internet browser

We recommend using a [Windows Mixed Reality immersive headset](hardware.md) with [Microsoft Edge](webvr-with-edge.md), but any of the compatible headsets and browsers listed on the [official site](https://webvr.info/) will do.

You can test whether your headset and browser support WebVR with these [samples](https://webvr.info/samples/).

Once you've determined that you can run WebVR, you can either develop using the [WebVR APIs](https://developer.mozilla.org/docs/Web/API/WebVR_API) directly in regular JavaScript, or you can use them on top of other frameworks like [Babylon.js](https://www.babylonjs.com/) or [WebGL](https://www.khronos.org/webgl/).

## See more

* [WebVR official site](https://webvr.info/)
* [WebVR API documentation on Mozilla Developer Network](https://developer.mozilla.org/docs/Web/API/WebVR_API
)
* [WebXR Device API specification](https://github.com/immersive-web/webxr)
* [Babylon.js official site](https://www.babylonjs.com/)