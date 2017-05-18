---
layout: post
title: 使用Appirater評分
date: 2014-11-25 18:10:19
tags: Appirater评分
excerpt: "Appirater评分."
comments: true
---

#使用 appirater为App添加让用户评分功能

##安裝 Appirater
首先，我们要先使用 CocoaPods 来安装 Appirater 这个套件，CocoaPods 是一个 Objective-c 的第三方套件管理工具，详细情况可以看 cocoapods 官网。在你的 iOS project 开发根目录下，创建一个叫做 Podfile 的档案(如果这是第一次使用 CocoaPods 的话)，并且在其中加入 Appirater 的 dependency :

	pod 'Appirater'

接着，再执行 `pods install` CocoaPods 就会自动帮你找到最新版本的 `Appirater` 并且安装了。安装完后点选新建立出来的 `workspace` 在 Xcode 中打开 project。

使用 Appirater
`Appirater` 使用起来非常简单，所要写的 code 几乎全部都在 `AppDelegate.m` 中，要开始使用它，先在你的 `AppDelegate.m` 的最上面 import 相关的 file :
{% highlight Objective-c %}
	#import "Appirater.h"
{% endhighlight %}
	
import 好了以后，就可以开始来进行 `Appirater` 的设定啰。在 `delegate` 的 `application:didFinishLaunchingWithOptions:` 最后面，加入以下的设定程式码:
{% highlight Objective-c %}
	[Appirater setAppId:@"YOUR_APP_ID"]; //# 设定 App Id，使用者如果点击评分的按钮的话，就会打开 AppStore，直接进到这个 App 页面
	[Appirater setDaysUntilPrompt:1]; //# 设定要使用几天后才会跳出询问视窗
	[Appirater setUsesUntilPrompt:10]; //# 设定要使用几次后才会跳出询问视窗
	[Appirater setSignificantEventsUntilPrompt:-1]; //# 设定使用者自订事件至少要发生几次后才会跳出询问视窗
	[Appirater setTimeBeforeReminding:2]; //# 设定使用者选择「稍后」之后几天才会再度跳出询问视窗
	[Appirater setDebug:YES]; //# 设定是否开启 debug 模式
	[Appirater appLaunched:YES]; //# 这行一定要放在这个 method 的最后面
{% endhighlight %}
通过上面程式码的设定，使用者可以很方便的去设置询问视窗要出现的条件，比方说设定至少要几天后，或是至少程式要开启几次后，才出现提示使用者评分的对话窗。其中 `setDebug` 可以设定是否开启 `debug` 模式，在 `debug` 模式中，每一次重开程式都会出现对话窗，如此开发者就可以很方便的去测试 `Appirater` 的功能了，不过在真的上线之前，记得要换回 `NO` 。这边都设定好后，执行应该就会出现对话窗啰。

实际运行结果如下图：
![alt](http://shaokanp.me/content/images/2014/May/IMG_1870-1.PNG)

转<http://shaokanp.me/appirater/>