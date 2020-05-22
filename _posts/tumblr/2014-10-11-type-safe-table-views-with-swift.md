---
layout: post
title: Type-safe Table Views with Swift
date: '2014-10-11T13:31:18-07:00'
tags: []
tumblr_url: https://blog.indragie.com/post/99740598364/type-safe-table-views-with-swift
---
I recently started writing an app in Swift, and one of the first things I needed to do was develop some infrastructure for implementing table view data sources. My goals were to decouple the data source implementation from the table view controller, reduce the boilerplate required to implement a typical data source, and to maximize type safety (since Swift is _all about_ that type safety).

The main challenge was building something that would bridge to Objective-C so that it could be used with `UITableView`. Since code that uses Swift-specific features like generics doesn’t bridge to Objective-C, achieving my goal of maximizing type safety turned out to be a non-trivial task. Read on to learn about my solution to this problem.

* * *

Since table views use data that is organized in sections, the first thing to do is to implement a `Section`:

<script src="https://gist.github.com/indragiek/1dbf039b416839006514.js?file=Section.swift"></script>

`Section` is implemented as a struct because it is a value type that contains no mutable state. Read [Andy Matuschak’s article](http://www.objc.io/issue-16/swift-classes-vs-structs.html) on structs and value types for more information on this topic.

Note that `Section` is a generic type, for the same reason why `Array` and `Dictionary` in Swift are generic types — to make them type safe by restricting their elements to a single type that is known at compile-time.

Next up is implementing the data source itself. From the methods defined in the `UITableViewDataSource` protocol, we understand that the table view data source has two primary responsibilites:

1. Tell the table view how much data it will display, in terms of sections and rows
2. Create and configure instances of `UITableViewCell` to display data

Since I wanted to allow for the possibility of also implementing a collection view data source at some point, I first defined a general data source for data organized in sections:

<script src="https://gist.github.com/indragiek/1dbf039b416839006514.js?file=SectionedDataSource.swift"></script>

This struct isn’t very useful at the moment, but the idea is that in the future, I may want to implement additional functionality that is common to anything that uses sections.

Now, `TableViewDataSource` is implemented as follows:

<script src="https://gist.github.com/indragiek/1dbf039b416839006514.js?file=TableViewDataSource.swift"></script>
- Since the data source needs to know the reuse identifier of the cell to be able to create an instance of it or dequeue it from the reuse pool, the `ReusableCell` protocol is defined so that a cell class conforming to it can define its own reuse identifier.
- `TableViewDataSource` is parametrized over `T`, the type of element that each section contains, and `U`, the type of a `UITableViewCell` subclass that conforms to `ReusableCell`.
- `CellConfigurator` is the type of a closure that configures a cell of type `U` using a section item of type `T`.

The next step is to implement the `UITableViewDataSource` methods. Easy, right? Not exactly. `TableViewDataSource` is a struct and also a generic type, which means that it does not bridge to Objective-C. This means that any methods declared inside it — like implementations of `UITableViewDataSource` methods — also do not bridge to Objective-C. This is obviously a problem, since this needs to bridge in order to be able to use it as the `dataSource` of a `UITableView`.

The solution to this problem is to strip away the bits that make it incompatible. In other words, the object implementing `UITableViewDataSource` needs to be, well… an object, not a struct, and it can’t be a generic type. In order to do this without losing type safety, we need to implement a way to transform instances of Swift-only types like `Section` and `TableViewDataSource` into instances of types that can be bridged to Objective-C.

First up is `Section`:

<script src="https://gist.github.com/indragiek/1dbf039b416839006514.js?file=SectionObjC.swift"></script>
- `SectionObjC` is a class that is very similar to the generic `Section` type, except that it gets rid of the need for a type argument by using `[AnyObject]`, which can safely be bridged to an `NSArray`. The `@objc` attribute is used tell the compiler that this class should be bridged to Objective-C.
- The type parameter `T` on `Section` has the new constraint that it must conform to `AnyObject`, since Objective-C doesn’t support structs and enums.
- The `toObjC()` method is defined on `Section` to transform it into an instance of `SectionObjC`.

`TableViewDataSource` needs a similar treatment:

<script src="https://gist.github.com/indragiek/1dbf039b416839006514.js?file=TableViewDataSourceObjC.swift"></script>

The changes here are essentially the same as the changes made to `Section`. A new class is defined in order to facilitate bridging by removing type parameters (using `AnyObject` instead), and a `toObjC()` function is defined on `TableViewDataSource` to transform it into an instance of the Objective-C compatible `TableViewDataSourceObjC`.

You might ask, doesn’t removing the type parameters from the \*ObjC classes also kill the type safety? In this case, the answer is no, because `SectionObjC` and `TableViewDataSourceObjC` are implementation details. `TableViewDataSourceObjC` is only exposed as “some object that conforms to `UITableViewDataSource`” so that it can be assigned as the `dataSource` of `UITableView`. In order to create an instance of `TableViewDataSourceObjC`, the API consumer must first create an instance of `TableViewDataSource`, which is completely type-safe.

The `UITableViewDataSource` methods can now be trivially implemented on `TableViewDataSourceObjC`:

<script src="https://gist.github.com/indragiek/1dbf039b416839006514.js?file=TableViewDataSource_UITVDS.swift"></script>

And we’re done! Here’s what basic usage looks like:

<script src="https://gist.github.com/indragiek/1dbf039b416839006514.js?file=ViewController.swift"></script>

This may not be a _perfect_ solution, but it ended up satisfying all of my goals, so I’m pretty happy with it. This also isn’t intended to cover all of the possible use cases for a table view — think of it as a case study on how common tasks in iOS development can be made much simpler and less error-prone by reimagining them using all of the power that Swift and static typing give us.

The full Xcode project is available [on GitHub](https://github.com/indragiek/SwiftTableViews).

