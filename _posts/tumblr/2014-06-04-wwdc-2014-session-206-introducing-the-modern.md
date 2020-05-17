---
layout: post
title: 'WWDC 2014 Session 206: Introducing the Modern WebKit API'
date: '2014-06-04T13:32:25-07:00'
tags: []
tumblr_url: https://blog.indragie.com/post/87809575549/wwdc-2014-session-206-introducing-the-modern
---
### Architecture

- API is identical on OS X and iOS.
- Uses the fast Nitro JS engine on iOS (unlike `UIWebView`), including fourth tier LLVM JIT.
- [`WKWebView`](https://developer.apple.com/library/prerelease/ios/documentation/WebKit/Reference/WKWebView_Ref/index.html#//apple_ref/occ/instp/WKWebView/navigationDelegate) has its own process(es), separate from the main app process.
- Each page gets its own process up to an unspecified limit, at which point pages start sharing processes.

### WKWebView

- [`WKWebViewConfiguration`](https://developer.apple.com/library/prerelease/ios/documentation/WebKit/Reference/WKWebViewConfiguration_Ref/index.html#//apple_ref/swift/cl/WKWebViewConfiguration) class encapsulates all configuration so that it can be shared among multiple instances of `WKWebView`.
- Properties (`title`, `URL`, `loading`, `estimatedProgress`) are KVO-compliant.

### Customizing Page Loading

- Hook into page loading, page actions, and network responses using methods in the [`WKWebViewNavigationDelegate`](https://developer.apple.com/library/prerelease/ios/documentation/WebKit/Reference/WKNavigationDelegate_Ref/index.html#//apple_ref/occ/intf/WKNavigationDelegate) protocol.

### Gestures

- `allowsBackForwardNavigationGestures` property enables left/right swipe gestures for navigation.
- Set `allowsMagnification` property on `NSScrollView` for double tap and pinch to zoom. `UIScrollView` does this automatically with no additional configuration.

### Customizing Webpage Content

- Use [`WKUserContentController`](https://developer.apple.com/library/prerelease/ios/documentation/WebKit/Reference/WKUserContentController_Ref/index.html#//apple_ref/doc/c_ref/WKUserContentController) (`userContentController` property on [`WKWebViewConfiguration`](https://developer.apple.com/library/prerelease/ios/documentation/WebKit/Reference/WKWebViewConfiguration_Ref/index.html#//apple_ref/swift/cl/WKWebViewConfiguration)) to inject user scripts into web pages and register handlers for script messages.
- User scripts (see `WKUserScript`) are scripts written in JS and are generally used to modify the DOM.
  - Debugged using the Safari web inspector.
- Script messages (see `WKScriptMessage`) are sent as JSON from the web page to your app and are automatically converted into Objective-C types.

* * *

The modern WebKit API, including `WKWebView` and all associated classes are part of the open source WebKit project. The source code for these classes can be found [here](https://github.com/WebKit/webkit/tree/master/Source/WebKit2/UIProcess/API/Cocoa).

