---
layout: post
title: "Swift, access control and testing"
date: 2014-08-23 22:04:31 -0300
comments: true
categories: [Swift, Tests]
---

Access control in Swift looks nice, but can give some trouble to find out how to make tests work smoothly. (The code in this post works with Xcode 6 beta 6.)

<!-- more -->

There are three [Access Control](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Inheritance.html#//apple_ref/doc/uid/TP40014097-CH17-XID_251) levels:

* *Public access* enables entities to be used within any source file from their defining module, and also in a source file from another module that imports the defining module. You typically use public access when specifying the public interface to a framework.
* *Internal access* enables entities to be used within any source file from their defining module, but not in any source file outside of that module. You typically use internal access when defining an app’s or a framework’s internal structure.
* *Private access* restricts the use of an entity to its own defining source file. Use private access to hide the implementation details of a specific piece of functionality.”

The default level is *Internal access*, this is fine, all your classes are visible only to your module, no imports, no external access. Although soon on later you will test your code, right?

First of all, XCTestCase are bundled on a different target and you don't include your classes to the test bundle, right? That's easy, just import the target that contains your class, if your target is called `MyProject` your test class will be like this:

```
import UIKit
import XCText
import MyProject

class MyProjectTests: XCTestCase {
    func testMyProject() {
        XCTAssert(true, "Pass")
    }
}
```

But this is not enough, do you remember that the default access level is `Inernal`? If you don't change it in the class that will be tested the compiler will say something like: `Use of unresolved identifier 'MyClass'`. To void this just set your class and all the methods and properties that will be tested to `public`:

```
public class MyClass: NSObject {
    public var prop: String
    public init() {}
}
```