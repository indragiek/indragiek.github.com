---
layout: post
title: 'WWDC 2014 Session 205: Creating Extensions for iOS and OS X, Part 1'
date: '2014-06-05T14:13:56-07:00'
tags: []
tumblr_url: https://blog.indragie.com/post/87910855304/wwdc-2014-session-205-creating-extensions-for-ios
---
- Extensions have a separate code signature, entitlements, and container from their parent app.
- They are not a form of app to app IPC and are accessed solely by Apple frameworks (not even Apple’s apps access them directly)

### Notification Center Widgets

- Widgets are view controllers — all of the same lifecycle and containment concepts apply.
  - Widget should be ready to display after `-viewWillAppear:`.
- Notification Center sets the view frame.
  - Height can be set using Autolayout constraints or by setting `UIViewController`’s [`preferredContentSize`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instp/UIViewController/preferredContentSize).
  - Content should animate alongside the height change.
    - On iOS, implement `-viewWillTransitionToSize:withTransitionCoordinator:` and call `-animateAlongsideTransition:completion:` from the [`UIViewControllerTransitionCoordinator`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerTransitionCoordinator_Protocol/index.html#//apple_ref/occ/intfm/UIViewControllerTransitionCoordinator/animateAlongsideTransition:completion:) protocol.
    - On OS X, implement `-viewWillTransitionToSize:`.
- Implement `-widgetPerformUpdateWithCompletionHandler:` from the [`NCWidgetProviding`](https://developer.apple.com/library/prerelease/ios/documentation/NotificationCenter/Reference/NCWidgetProviding_Protocol/index.html#//apple_ref/occ/intf/NCWidgetProviding) protocol to be notified when to update the widget.
- New “Today Extension” Xcode template on both iOS and OS X for adding a widget target.
- Use `UIViewController`’s [`extensionContext`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/#//apple_ref/occ/instp/UIViewController/extensionContext) (see [`NSExtensionContext`](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSExtensionContext_Class/)) to call back to the main app from the widget.

### Share Extensions

- Set `CFBundleName` in the extension bundle’s Info.plist to set the display name.
- Set activation rules (ie. the rules that determine when your share extension shows up in the activity picker) by setting a predicate string for the `NSExtension` -\> `NSExtensionAttributes` -\> `NSExtensionActivationRule` key in Info.plist.
  - Simpler rules can be expressed using one of the [condensed rules](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html#//apple_ref/doc/uid/TP40014214-CH21-SW8) instead of a predicate.
- Extensions with custom UI can inherit from [`UIViewController`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/) or [`NSViewController`](https://developer.apple.com/library/mac/documentation/cocoa/reference/NSViewController_Class/Introduction/Introduction.html).
- Extensions that want the standard share sheet UI can inherit from [`SLComposeServiceViewController`](https://developer.apple.com/library/prerelease/ios/documentation/Social/Reference/SLComposeServiceViewController_Class/).
  - Override [`-presentationAnimationDidFinish`](https://developer.apple.com/library/prerelease/ios/documentation/Social/Reference/SLComposeServiceViewController_Class/#//apple_ref/occ/instm/SLComposeServiceViewController/presentationAnimationDidFinish) to start heavy lifting after presentation is complete.
  - Override [`-isContentValid`](https://developer.apple.com/library/prerelease/ios/documentation/Social/Reference/SLComposeServiceViewController_Class/#//apple_ref/occ/instm/SLComposeServiceViewController/isContentValid) to set the enabled status of the Post button.
    - Call [`-validateContent`](https://developer.apple.com/library/prerelease/ios/documentation/Social/Reference/SLComposeServiceViewController_Class/#//apple_ref/occ/instm/SLComposeServiceViewController/validateContent) to manually trigger an update of the Post button status.
  - Set [`charactersRemaining`](https://developer.apple.com/library/prerelease/ios/documentation/Social/Reference/SLComposeServiceViewController_Class/#//apple_ref/occ/instp/SLComposeServiceViewController/charactersRemaining) property to update UI to reflect how many characters are left.
  - Override [`-didSelectPost`](https://developer.apple.com/library/prerelease/ios/documentation/Social/Reference/SLComposeServiceViewController_Class/#//apple_ref/occ/instm/SLComposeServiceViewController/didSelectPost) to implement upload logic when user taps Post button.
  - Override [`-configurationItems`](https://developer.apple.com/library/prerelease/ios/documentation/Social/Reference/SLComposeServiceViewController_Class/#//apple_ref/occ/instm/SLComposeServiceViewController/configurationItems) to provide an array of [`SLComposeSheetConfigurationItem`](https://developer.apple.com/library/prerelease/ios/documentation/Social/Reference/SLComposeSheetConfigurationItem_Class/index.html#//apple_ref/occ/cl/SLComposeSheetConfigurationItem) objects for displaying custom service-specific settings.
- Get data to upload using `NSExtensionContext`’s [`inputItems`](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSExtensionContext_Class/#//apple_ref/occ/instp/NSExtensionContext/inputItems) property.
- Must use [`NSURLSession`](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSession_class/) with a background session configuration (see [`+[NSURLSessionConfiguration backgroundSessionConfiguration:]`](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSURLSessionConfiguration_class/)) for uploading data.
- Call [`-[NSExtensionContext completeRequestReturningItems:completionHandler:]`](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSExtensionContext_Class/#//apple_ref/occ/instm/NSExtensionContext/completeRequestReturningItems:completionHandler:) after starting the upload to close the share sheet, or [`-[NSExtensionContext cancelRequestWithError:]`](https://developer.apple.com/library/prerelease/ios/documentation/Foundation/Reference/NSExtensionContext_Class/#//apple_ref/occ/instm/NSExtensionContext/cancelRequestWithError:) if the user cancelled.

* * *

Complete documentation can be found in the [App Extension Programming Guide](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/).

