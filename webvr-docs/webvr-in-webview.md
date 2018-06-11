---
title: WebVR in WebView
description: Learn how to add WebVR to a WebView control.
author: eliotcowley
ms.author: elcowle
ms.date: 06/11/2018
ms.topic: article
ms.prod: microsoft-edge
ms.technology: webvr
keywords: WebVR, webview, uwp
---

# WebVR in WebView

As of the Windows 10 April 2018 Update (version 1803, build 17134), WebVR is supported in WebView controls in Windows 10 apps. The WebView control enables you to host web content in your Windows 10 app. See [Microsoft Edge WebView for Windows 10 apps](https://docs.microsoft.com/microsoft-edge/webview) for more information about WebView.

## Add WebVR support to a WebView control

In order for your WebView control to host WebVR content, there are a few steps you'll have to take.

1. **Initialize the WebView programmatically to run in a separate process.** You'll have to declare, initialize, and add it to the UI in the code-behind, rather than in the XAML.

2. Add a **PermissionRequested** event handler for the WebView. In it, allow the permission request.

The following sample declares and initializes a WebView, handles the **PermissionRequested** event, adds the WebView to the UI, and navigates to a WebVR site.

```csharp
WebView webView;

public MainPage()
{
    this.InitializeComponent();
    webView = new WebView(WebViewExecutionMode.SeparateProcess);
    webView.PermissionRequested += MyWebView_PermissionRequested;
    MyGrid.Children.Add(webView);
    Grid.SetRow(webView, 1);
    webView.Navigate("https://webvr.info/samples/03-vr-presentation.html");
}

private void MyWebView_PermissionRequested(
    WebView sender, WebViewPermissionRequestedEventArgs args)
{
    args.PermissionRequest.Allow();
}
```

```javascript
var wvprocess = new MSWebViewProcess();

// WebViews created with the same MSWebViewProcess object share the same process
wvprocess.CreateWebViewAsync().then(function (webview) {
    webview.navigate("https://webvr.info/samples/03-vr-presentation.html");
    document.body.appendChild(webview);
    webview.addEventListener("MSWebViewPermissionRequested", e => {
        e.permissionRequest.allow();
    });
});
```

## See also

* [Microsoft Edge WebView for Windows 10 apps](https://docs.microsoft.com/microsoft-edge/webview)
* [WebView Class (Windows.UI.Xaml.Controls)](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview)
* [WebVR Developer's Guide](https://docs.microsoft.com/microsoft-edge/webvr/)