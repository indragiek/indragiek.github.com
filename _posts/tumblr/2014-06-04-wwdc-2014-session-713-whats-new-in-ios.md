---
layout: post
title: 'WWDC 2014 Session 713: What''s New in iOS Notifications'
date: '2014-06-04T17:33:39-07:00'
tags: []
tumblr_url: https://blog.indragie.com/post/87830351449/wwdc-2014-session-713-whats-new-in-ios
---
### User Notifications

- Local notifications now require user approval as well.
- Apps must register notification settings (see [`UIUserNotificationSettings`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIUserNotificationSettings_class/index.html#//apple_ref/occ/cl/UIUserNotificationSettings)) by calling `-[UIApplication registerUserNotificationSettings:]`.

### Notification Actions

- Two new actions when swiping to the left on the lock screen or notification center, or when swiping down on a notification banner.
- Create actions using the [`UIMutableUserNotificationAction`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableUserNotificationAction_class/index.html) class.
- Use `UIMutableUserNotificationCategory` to group actions for particular contexts (default and minimal).
  - Minimal is used in lock screen, notification center, and banners.
  - Default is used in notification alerts.

### Remote Notifications

- Need to include `category` identifier in push payload.
- Previous size limit of 256 bytes for a payload has been increased to 2 KB.
- Handle them using [`UIApplicationDelegate`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_class/index.html#//apple_ref/occ/instp/UIApplication/delegate)’s `-application:handleActionWithIdentifier:forRemoteNotification:completionHandler:`
- Silent notifications require `remote-notification` in the `UIBackgroundModes` array in Info.plist.
- Enabled by default, can be disabled in Settings, user is not prompted for approval until a notification is posted.
- `-[UIApplication registerForRemoteNotifications]` is the new method for registering for remote notifications.

### Local Notifications

- Set category identifier using `category` property on `UILocalNotification`.
- Handle them using `UIApplicationDelegate`’s `-application:handleActionWithIdentifier:forLocalNotification:completionHandler:`

### Location Notifications

- Need to register with Core Location using `-[CLLocationManager requestWhenInUseAuthorization]`, which requests permission for tracking user location when the app is running in the foreground.
  - Define `NSLocationWhenInUseUsageDescription` key in Info.plist with a string value to set the usage text that appears in the authorization alert.
- Handle [`CLLocationManagerDelegate`](https://developer.apple.com/library/prerelease/ios/documentation/CoreLocation/Reference/CLLocationManager_class/index.html#//apple_ref/occ/instp/CLLocationManager/delegate)’s `-locationManager:didChangeAuthorizationStatus:` callback to know when to schedule location based notifications.
- `UILocalNotification` has new properties `region` and `regionTriggersOnce` for location based notifications, schedule them like any other local notification.
