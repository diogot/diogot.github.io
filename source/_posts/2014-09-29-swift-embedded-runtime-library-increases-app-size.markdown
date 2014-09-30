---
layout: post
title: "Swift embedded runtime library increases app size"
date: 2014-09-29 23:44:55 -0300
comments: true
categories: [Swift, iOS]
---

Since beginning Swift attracted a lot of attention, one of many questions was: How Apple will guarantee backwards compatibility in future Swift releases?

The response is cited a few times at Swift Blog, like the [post of 1.0 release](https://developer.apple.com/swift/blog/?id=14):

> Because your apps today embed a version of the Swift GM runtime, they will continue to run well into the future.

But one thing, so far, I don't see people talking about is how this embedded library affect the size of an app? 

<!-- more -->

The easiest way to discover is to create an empty project and compare the size. 
I created two *Single View Application* projects using on Xcode 6.0.1. On the first image is the bundle directory of Objective-C app and on the second the Swift one.

{% img center /images/swift-lib-overhead.jpg 518 338 'App bundle files and sizes' %}

The Objective-C only app bundle takes only 192 KB and the Swift app bundle 8.6 MB. The Swift binary is smaller but the runtime frameworks can be a huge overhead for many apps!

On the second post of Swift Blog talk about [Compatibility](https://developer.apple.com/swift/blog/?id=2):

> As Swift changes, those frameworks will be incompatible with the rest of your app. When the binary interface stabilizes in a year or two, the Swift runtime will become part of the host OS and thi s limitation will no longer exist.

So for at least one year if you want to use Swift in your apps, you will have to accept this *little* overhead.