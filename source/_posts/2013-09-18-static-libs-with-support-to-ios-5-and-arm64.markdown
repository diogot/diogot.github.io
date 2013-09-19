---
layout: post
title: "Static libs with support to iOS 5 and arm64"
date: 2013-09-18 12:36
comments: true
categories: [iOS, Xcode, arm64]
---

With the launch of iPhone 5s Apple brings the 64-bit architecture to mobile, this brings new possibilities for the iOS platform. But there is a restriction imposed by Apple, only projects with deployment target to iOS 6.0 or higher can produce fat binaries including arm64. So the apps developer should choose between support iOS 5 and earlier or arm64, although SDK developers shouldn't be obligated to decide for your users! It's possible to produce one fat binary with all the architectures, so let's see one way to do it.

<!-- more -->

There is a [discussion](https://devforums.apple.com/message/887883#887883) on Developer Forums where an Apple developer explains that this restriction is due to iOS 5 are not prepared to handle the fat binary headers that contain 64-bit code. Although static libraries aren't handled by iOS but by the linker that came with Xcode and Xcode 5 linker obviously can handle 64-bit code!

The idea is pretty simple, build the different architectures separated then bind them using [lipo](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/lipo.1.html).
I know that is possible to do this using directly the Xcode, but I prefer to do it via command line (or Rakefile).

First build the binary with `arm` using `xcodebuild`:

``` bash Build arm
xcodebuild -project 'StaticLibDemo.xcodeproj' -configuration 'Release' -sdk 'iphoneos7.0' clean build ARCHS='armv7 armv7s' IPHONEOS_DEPLOYMENT_TARGET='5.0' TARGET_BUILD_DIR='./build-arm' BUILT_PRODUCTS_DIR='./build-arm'
```

Note that you must set `IPHONEOS_DEPLOYMENT_TARGET='5.0'` and ARCHS='armv7 armv7s', it's recommended to set build and product dirs to make the things more clear, take a look at [Build Setting Reference](https://developer.apple.com/library/ios/documentation/DeveloperTools/Reference/XcodeBuildSettingRef/1-Build_Setting_Reference/build_setting_ref.html) for more details what this flags means.

Next build for `arm64`:

``` bash Build arm64
xcodebuild -project 'StaticLibDemo.xcodeproj' -configuration 'Release' -sdk 'iphoneos7.0' clean build ARCHS='arm64' IPHONEOS_DEPLOYMENT_TARGET='7.0' TARGET_BUILD_DIR='./build-arm64' BUILT_PRODUCTS_DIR='./build-arm64'
```

Note the difference on `ARCHS` and `IPHONEOS_DEPLOYMENT_TARGET`. We also need to build for simulator, in this case we have to change the sdk to `iphonesimulator7.0` and build in two steps first for `i386`:

``` bash Build i386
xcodebuild -project 'StaticLibDemo.xcodeproj' -configuration 'Release' -sdk 'iphonesimulator7.0' clean build ARCHS='i386' IPHONEOS_DEPLOYMENT_TARGET='5.0' TARGET_BUILD_DIR='./build-i386' BUILT_PRODUCTS_DIR='./build-i386'
```

Now the tricky part! If you just change the `ARCHS` to `x86_86` depending on your Xcode setting you will got an error like: "x86_64 is not a valid arch". To avoid this just add `VALID_ARCHS='x86_64'`:

``` bash Build x86_64
xcodebuild -project 'StaticLibDemo.xcodeproj' -configuration 'Release' -sdk 'iphonesimulator7.0' clean build ARCHS='x86_64' VALID_ARCHS='x86_64' IPHONEOS_DEPLOYMENT_TARGET='7.0' TARGET_BUILD_DIR='./build-x86_64' BUILT_PRODUCTS_DIR='./build-x86_64'
```

Finally we just have to create a fat binary with all the 5 architectures:

``` bash Lipo
lipo -create './build-arm/libStaticLibDemo.a' './build-arm64/libStaticLibDemo.a' './build-i386/libStaticLibDemo.a' './build-x86_64/libStaticLibDemo.a' -output 'libStaticLibDemo.a'
```

I created a working example of this, you can get it [here](https://github.com/diogot/StaticLibDemo).
