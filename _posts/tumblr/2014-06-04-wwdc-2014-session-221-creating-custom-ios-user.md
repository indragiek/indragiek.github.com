---
layout: post
title: 'WWDC 2014 Session 221: Creating Custom iOS User Interfaces'
date: '2014-06-04T19:28:00-07:00'
tags: []
tumblr_url: https://blog.indragie.com/post/87840186259/wwdc-2014-session-221-creating-custom-ios-user
---
### Spring Animations

- [`-[UIView animateWithDuration:delay:usingSpringWithDamping:
initialSpringVelocity:options:animation:completion]`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_class/index.html#//apple_ref/occ/clm/UIView/animateWithDuration:delay:usingSpringWithDamping:initialSpringVelocity:options:animations:completion:)
  - Can be applied to any animatable property.

### Vibrancy and Blur

- Continue using [`-[UIView drawViewHierarchyInRect:afterScreenUpdates:]`](https://developer.apple.com/library/ios/documentation/uikit/reference/uiview_class/uiview/uiview.html#//apple_ref/occ/instm/UIView/drawViewHierarchyInRect:afterScreenUpdates:) for static blurs.
- [`UIVisualEffectView`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIVisualEffectView/index.html#//apple_ref/occ/cl/UIVisualEffectView) is a new API for creating live blurs and vibrancy effects.
  - Vibrancy is used to make content visible when the background is blurred.
  - Blurs can be tinted by setting the `backgroundColor` of the `contentView`
  - Changing the `alpha` will result in the blur being dropped.
  - Can not be placed in a view hierarchy that contains masks.
- [`UIBlurEffect`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIBlurEffect_Ref/index.html#//apple_ref/occ/cl/UIBlurEffect)
  - Has 3 styles: dark, light, extra light
  - Not just a simple Gaussian blur. Three steps:
    1. Downsample
    2. Modify colors
    3. Compute the blur
  - Rendered offscreen.
- [`UIVibrancyEffect`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIVibrancyEffect/index.html#//apple_ref/occ/cl/UIVibrancyEffect)
  - Used as a subview of or layered on top of a `UIVisualEffectView` that uses a `UIBlurEffect` to make content (e.g. text) more vivid and legible.
  - Also rendered offscreen, takes even more time than blur.

### Shape Layers

- [`CAShapeLayer`](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CAShapeLayer_class/) is used to create animatable bezier paths.
  - Rasterizes shape layer on CPU and sends the rasterized layer to the render server
  - Expensive in CPU time, be cautious about frequent changes.

### Dynamic Core Animation Behaviours

- Actions can be used to implement dynamic animation behaviour based on the context.
  - `-actionForLayer:forKey:` is called on the [`CALayerDelegate`](https://developer.apple.com/library/prerelease/ios/documentation/QuartzCore/Reference/CALayerDelegate_protocol/) when an animatable property is set.
    - Return an object that conforms to the [`CAAction`](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CAAction_protocol/index.html#//apple_ref/occ/intf/CAAction) protocol to run custom animations.
