---
layout: post
title: 关于XCode自带Debug Memory Graph监测对象释放情况
date: 2016-09-10 18:24:23
tags: 对象释放
excerpt: "监测对象释放情况."
comments: true
---


>Xcode8的调试技能又增加了一个黑科技：Memory Graph。简单的说就是可以在运行时将内存中的对象生成一张图。但是我基本不看，嘻嘻！我主要看的是当我从A界面push到B界面，又pop回A界面后，A界面上所有的对象，这时候我会检查有没有没有被释放掉的对象

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/4241428-7863dfac6d518dea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
点击这个就可以看了，具体的对象列表，看你的左手边

开发老大有时候会要求app运行时的内存大小，而造成app占用内存过大的原因我总结了几点
>1. 首先就是循环引用导致的controller 无法销毁 (每个控制器都需要写delloc，良好的代码风格)
2. 有的时候控制器销毁了，但是里面的视图也会存在循环引用，导致一些视图无法销毁
3. 控制器销毁时没有移除通知
4. 过多的绘制图片，调用c的方法
5. 等等......不一一列举 以上大家平时容易遇到的

>就以上问题，1，2是最让人头疼的，那就是找出循环引用的地方！WTF！！笨的方法就是一行一行看，一行一行注释、打开！

秋豆莫代！废话说了一堆！上图

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/4241428-aec15f3cdb7ec3ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个是控制器B里面我放了一个循环引用(已经提示了，自行忽略！)，pop回去的时候delloc 一定不会走的

3秒后，背景红了，证明block执行了，看下右边的对象列表，嗯！没错 两个控制器

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/4241428-be729769ca6bee92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在我要pop回去了 


![Paste_Image.png](http://upload-images.jianshu.io/upload_images/4241428-edb9bbdf96e69150.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
还是两个控制器的，这就证明 B控制器没有被释放,delloc根本就没走

>假如B控制器里还有其他的视图存在循环引用，那么我们就可以直接通过Debug Memory Graph来检查哪个视图没有被释放掉，就不用一行一行来注释来，这为我们的开发节约了很大一部分时间!

>当然，良好的代码风格也是避免一些低级错误发生的很好的习惯！
