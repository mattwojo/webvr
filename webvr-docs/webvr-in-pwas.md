---
title: WebVR in Progressive Web Apps
description: Learn how to add WebVR to your Progressive Web Apps.
author: eliotcowley
ms.author: elcowle
ms.date: 06/05/2018
ms.topic: article
ms.prod: microsoft-edge
ms.technology: webvr
keywords: WebVR, progressive web app, pwa
---

# WebVR in Progressive Web Apps

As of the Windows 10 April 2018 Update (version 1803, build 17134), WebVR is supported in [Progressive Web Apps](https://docs.microsoft.com/microsoft-edge/progressive-web-apps) (PWAs). PWAs combine the best of the web and native apps, allowing you to take your existing websites and publish them to the Microsoft Store as Windows 10 applications. You can make your PWAs even better by adding engaging WebVR functionality, providing deeper, more immersive experiences.

## Add WebVR to a PWA with Babylon.js

[Babylon.js](https://www.babylonjs.com/) makes it easy to create WebVR experiences, and in the following example we will use it to add a simple WebVR experience to an existing PWA. We will be using the default project that is created in Visual Studio with the **Basic Node.js Express 4 Application** template. While we will be using Node.js, you can apply this to other web application frameworks as well.

### Prerequisites

Though you can use any web development IDE and framework you prefer, to follow along with this tutorial, you will need the following:

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) (any edition&mdash;Community is free)
    * When installing, make sure to select the **Universal Windows Platform development** and **Node.js development** workloads. If you've already installed Visual Studio 2017, you can open the **Visual Studio Installer** and click **Modify** under your installation to install the workloads.

### Create the web app

We will be using the default project created with the **Basic Node.js Express 4 Application** template. To create it, in Visual Studio:

1. Select **File > New > Project...**

2. In the **New Project** window, in the left sidebar, select **Installed > JavaScript > Node.js > Basic Node.js Express 4 Application**. Name your app, select a **Location**, and click **OK**.

3. Press **F5** to run the app, and you should see a simple webpage appear.

    ![Express; Welcome to Express](img/express-webpage.png)

### Add Babylon.js

Now we will add a simple 3D experience to our web app using Babylon.js.

1. In the **Solution Explorer**, under your project, find the **public > javascripts** folder. Right-click it and select **Add > New Item...**

2. In the **Add New Item** window, select **JavaScript file**, give it a **Name** (for example, **main.js**), and click **Add**.

3. Add the following code to the file. This code initializes Babylon.js and creates a simple scene:

    ```js
    // Get the canvas element 
    var canvas = document.getElementById("renderCanvas");

    // Generate the BABYLON 3D engine
    var engine = new BABYLON.Engine(canvas, true);

    var createScene = function () {

        // Create the scene space
        var scene = new BABYLON.Scene(engine);

        // Add a camera to the scene and attach it to the canvas
        var camera = new BABYLON.ArcRotateCamera("Camera", Math.PI / 2, Math.PI / 2, 2, BABYLON.Vector3.Zero(), scene);
        camera.attachControl(canvas, true);

        // Add lights to the scene
        var light1 = new BABYLON.HemisphericLight("light1", new BABYLON.Vector3(1, 1, 0), scene);
        var light2 = new BABYLON.PointLight("light2", new BABYLON.Vector3(0, 1, -1), scene);

        // This is where you create and manipulate meshes
        var sphere = BABYLON.MeshBuilder.CreateSphere("sphere", {}, scene);

        return scene;
    };

    var scene = createScene(); //Call the createScene function

    engine.runRenderLoop(function () { // Register a render loop to repeatedly render the scene
        scene.render();
    });

    window.addEventListener("resize", function () { // Watch for browser/canvas resize events
        engine.resize();
    });
    ```

4. In **layout.pug** (in the **views** folder), add the following code to the `head` block. This adds the scripts necessary for Babylon.js:

```pug
script(src="https://cdn.babylonjs.com/babylon.js")
script(src="https://preview.babylonjs.com/loaders/babylonjs.loaders.min.js")
script(src="https://code.jquery.com/pep/0.4.1/pep.js")
```

5. Replace the code in the `body` block with the following code:

```pug
canvas(id="renderCanvas", touch-action="none")
script(src='/javascripts/main.js')
```

6. In **main.css** (under **public > stylesheets**), remove the `body` block and add the following code:

```css
html, body {
    overflow: hidden;
    width: 100%;
    height: 100%;
    margin: 0;
    padding: 0;
}

#renderCanvas {
    width: 100%;
    height: 100%;
}
```

7. In **index.js** (in the **routes** folder), replace the `res.render` call (line 7) with the following:

```js
res.render('index');
```

8. In **index.pug** (in the **views** folder), remove lines 3-5 (the `block` section).

9. Run the app, and you should see a 3D sphere appear. You should also be able to rotate around it with the left mouse button.

    ![Gray 3D sphere floating in blue background](img/babylon-sphere.png)

### Add WebVR

Now that we have a 3D Babylon.js experience working in our PWA, we can easily add WebVR support.

1. In **main.js**, replace the `createScene` function (should start on line 7) with the following code:

```js
var createScene = function () {

    // Create the scene space
    var scene = new BABYLON.Scene(engine);

    // Add a camera to the scene and attach it to the canvas
    var camera = new BABYLON.WebVRFreeCamera("Camera", new BABYLON.Vector3(0, 0, 0), scene);

    scene.onPointerDown = function () {
        scene.onPointerDown = undefined;
        camera.attachControl(canvas, true);
    };

    // Add lights to the scene
    var light1 = new BABYLON.HemisphericLight("light1", new BABYLON.Vector3(1, 1, 0), scene);
    var light2 = new BABYLON.PointLight("light2", new BABYLON.Vector3(0, 1, -1), scene);

    // This is where you create and manipulate meshes
    var sphere = BABYLON.MeshBuilder.CreateSphere("sphere", {}, scene);
    sphere.position.z = 10;

    return scene;
};
```

Basically, we are changing the camera to work in VR (the `WebVRFreeCamera`), making it so the user must click the screen to start the experience, and repositioning the sphere to be in front of the viewer.

2. Run the app. At first, you will just see a blank screen. Attach your Windows Mixed Reality immersive headset, open the Mixed Reality Portal, and click the screen with the mouse. Put on your headset, and you should see the scene we just created in VR!

## See also

* [Babylon.js](https://www.babylonjs.com/)
* [WebVR Developer's Guide](index.md)
* [Progressive Web Apps on Windows](https://docs.microsoft.com/microsoft-edge/progressive-web-apps
)