---
layout: post
title: 'WWDC 2014 Session 413: Debugging with Xcode 6'
date: '2014-06-04T16:56:21-07:00'
tags: []
tumblr_url: https://blog.indragie.com/post/87826895479/wwdc-2014-session-413-debugging-with-xcode-6
---
### Queue Debugging

- Xcode 6 can show backtraces for enqueued blocks across GCD queues and threads in the Debug navigator.
- In the Queues view, it also shows the pending blocks for a queue in addition to the blocks that are already executing.
  - Debug menu \> Debug Workflow \> Always Show Pending Blocks in Queue
- Shortcut: hold down Option key and click the disclosure triangle to the left of the process info to expand all the thread backtraces.

### View Debugging

- View debugger button is on the Debug toolbar (bottom of the window) to the left of the “Simulate location” button.
- Third view mode in the Debug navigator (in addition to thread and queue views) for showing the UI hierarchy.
  - You can filter views using the search field!
- Attributes inspector shows view attributes but they are read-only.
- Size inspector shows a list of all the layout constraints.

### Integrating with Quick Look

- Xcode 6 supports Quick Look for `UIView` and `NSView`.
- Override `-debugQuickLookObject` in your own objects and return one of the supported types to add support for Quick Look.
- More documentation [here](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/CustomClassDisplay_in_QuickLook/CH01-quick_look_for_custom_objects/CH01-quick_look_for_custom_objects.html).
