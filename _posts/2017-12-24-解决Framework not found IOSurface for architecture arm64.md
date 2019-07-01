---
layout: post
title: 解决Framework not found IOSurface for architecture arm64.md
date: 2017-12-24 10:47:39
tags: Framework not found IOSurface for architecture arm64
excerpt: "ssk key."
comments: true
---

#Framework not found IOSurface for architecture arm64

使用xcode9编译工程后，再切换到xcode8时偶尔会出现这个问题。

解决方法：

第一步：
  进入xcode9 Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk/System/Library/Frameworks/
路径。
第二步：
  将FileProvider.framework 和 IOSurface.framework复制到xcode8对应的目录下
第三步：
  cmd+K clean项目重新编译

原文：
https://stackoverflow.com/questions/44450673/framework-not-found-iosurface-for-architecture-arm64
