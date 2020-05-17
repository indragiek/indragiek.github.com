---
layout: post
title: 'WWDC 2014 Session 226: What''s New in Table and Collection Views'
date: '2014-06-05T18:34:42-07:00'
tags: []
tumblr_url: https://blog.indragie.com/post/87931066309/wwdc-2014-session-226-whats-new-in-table-and
---
### Adopting Dynamic Type

- Default labels already use dynamic type.
- Use [`+[UIFont preferredFontForTextStyle:]`](https://developer.apple.com/library/ios/documentation/uikit/reference/UIFont_Class/Reference/Reference.html#//apple_ref/occ/clm/UIFont/preferredFontForTextStyle:) for custom labels.

### Self-sizing Table View Cells

- Two options for self-sizing table cells:
  1. Autolayout constraints — add constraints to `cell.contentView`.
  2. Manual sizing — override `-sizeThatFits:`.
- Set `UITableView`’s [`estimatedRowHeight`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITableView_Class/#//apple_ref/occ/instp/UITableView/estimatedRowHeight) instead of `rowHeight` and set `rowHeight` to `UITableViewAutomaticDimension` on iOS 8 for self-sizing table cells.
  - In the current seed, table views unarchived from a NIB have their `rowHeight` property set to a constant height by default. This will change to `UITableViewAutomaticDimension` in a future seed.

### Self-sizing Collection View Cells

- Same as table cells, use constraints or override `-sizeThatFits:`.
- Override [`-[UICollectionReusableView preferredLayoutAttributesFittingAttributes:]`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UICollectionReusableView_class/index.html#//apple_ref/occ/instm/UICollectionReusableView/preferredLayoutAttributesFittingAttributes:) to adjust layout attributes determined by the `UICollectionViewLayout`.
- New [`estimatedItemSize`](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UICollectionViewFlowLayout_class/#//apple_ref/occ/instp/UICollectionViewFlowLayout/estimatedItemSize) property on `UICollectionViewFlowLayout`, equivalent to `estimatedRowHeight` on `UITableView`.

### Invalidation Contexts

- Fine grained invalidation methods:

- Inform the collection view of a content-size change using the `contentSizeAdjustment` and `contentOffsetAdjustment` properties on `UICollectionViewLayout` (deltas).

