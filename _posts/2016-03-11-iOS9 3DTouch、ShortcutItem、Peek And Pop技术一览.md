---
layout : post
title: iOS9 3DTouch、ShortcutItem、Peek And Pop技术一览
date: 2016-03-11 10:53:51
tags: Blog
excerpt: "iOS9 新功能预览"
comments: true
---
# iOS9 3DTouch、ShortcutItem、Peek And Pop技术一览

## 3DTouch
![](https://developer.apple.com/ios/3d-touch/images/pressure-sensitivity_2x.jpg)

## UITouch类里API的变化
### iOS9中添加的属性
###### altitudeAngle

* 当笔平行于平面时,该值为0

* 当笔垂直于平面时,该值为Pi / 2

###### estimatedProperties

* 当前触摸对象估计的触摸特性

* 返回值是UITouchPropertyies

###### updatedProperties

* 当前触摸对象已经更新的触摸特性

* 返回值是UITouchPropertyies

###### estimationUpdateIndex

* 当每个触摸对象的触摸特性发生变化时，该值将会单独增加

* 返回值是NSNumber

### iOS9中添加的方法
###### - PreciseLocationInView:

* 当前触摸对象的坐标

###### - PrecisePreviousLocationInView:

* 当前触摸对象的前置坐标

###### - azimuthAngleInview:

* 沿着x轴正向的方位角

* 当与x轴正向方向相同时,该值为0

* 当view参数为nil时，默认为keyWindow

###### - azimuthUnitVectorInView:

* 当前触摸对象的方向上的单位向量

* 当view参数为nil时，默认为keyWindow

### UIForceTouchCapability
###### UIForceTouchCapabilityUnknown

* 不能确定是否支持压力感应

###### UIForceTouchCapabilityUnavailable

* 不能支持压力感应

###### UIForceTouchCapabilityAvailable

* 可以支持压力感应

### UITouchType

###### UITouchTypeDirect

* 垂直的触摸类型

###### UITouchTypeIndirect

* 非初值的触摸类型

###### UITouchTypeStylus

* 水平的触摸类型

### UITouchProperties
###### UITouchPropertyForce

## ShortcutItem

![](https://developer.apple.com/ios/3d-touch/images/quick-actions_2x.jpg)

### 静态方式

* 打开Info.plist文件
* 在对应UIApplicationShortcutItems关键字下添加item

### 动态方式

#### 修改当前应用程序的某个shortcutItem

      //获取第0个shortcutItem  
      id oldItem = [existingShortcutItems objectAtIndex: 0];  
      //将旧的shortcutItem改变为可修改类型shortcutItem  
      id mutableItem = [oldItem mutableCopy];  
      //修改shortcutItem的显示标题  
      [mutableItem setLocalizedTitle: @“Click Lewis”];
      
#### 获取当前应用程序的shortcutItems

      //获取当前应用程序对象  
      UIApplication *app = [UIApplication sharedApplication];  
      //获取一个应用程序对象的shortcutItem列表  
      id existingShortcutItems = [app shortcutItems];
      
#### 重置当前应用程序的shortcutItems

      //根据旧的shortcutItems生成可变shortcutItems数组  
      id updatedShortcutItems = [existingShortcutItems mutableCopy];  
      //修改可变shortcutItems数组中对应index下的元素为新的shortcutItem  
      [updatedShortcutItems replaceObjectAtIndex: 0 withObject: mutableItem];  
      //修改应用程序对象的shortcutItems为新的数组  
      [app setShortcutItems: updatedShortcutItems];
### 创建一个新的UIApplicationShortcutItem
* 初始化函数

 - -initWithType:localizedTitle:localizedSubtitle:icon:userInfo:
 
 - -initWithType:localizedTitle:
* 属性

 - -localizedTitle:NSString

 - -localizedSubtitle:NSString

 - -type:NSString

 - -icon:UIApplicationShortcutIcon

 - -userInfo:NSDictionary

 - 只有只读特性，想要进行修改时，需要通过mutableCopy方法转变为NSMutableApplicationShortcutItem
 
### 创建一个新的Item图标
* 初始化函数

 + +iconWithType:

 + +iconWithTemplateImageName:

 + +iconWithContact:

### 当程序启动时
* 判断launchOptions字典内的UIApplicationLaunchOptionsShortcutItemKey是否为空
* 当不为空时,application:didFinishLaunchWithOptions方法返回false，否则返回true
* 在application:performActionForShortcutItem:completionHandler方法内处理点击事件

## Peek and Pop

![](https://developer.apple.com/ios/3d-touch/images/peek-and-pop_2x.jpg)

### 注册预览功能的代理对象和源视图

#### 代理对象需要接受UIViewControllerPreviewingDelegate协议

    @interface RootVC<UIViewControllerPreviewingDelegate>  
    {}  
    @end
    
#### 代理对象实现协议内的Peek和Pop方法

     @implementation RootVC  
  	  - (UIViewController *)previewingContext:(id<UIViewControllerPreviewing>)context viewControllerForLocation:(CGPoint) point  
    {  
        UIViewController *childVC = [[UIViewController alloc] init];  
        childVC.preferredContentSize = CGSizeMake(0.0f,300f);  

        CGRect rect = CGRectMake(10, point.y - 10, self.view.frame.size.width - 20,20);  
        context.sourceRect = rect;  
        return childVC;  
    }  
    
    - (void)previewContext:(id<UIViewControllerPreviewing>)context commitViewController:(UIViewController*)vc  
    {  
        [self showViewController:vc sender:self];  
    }  
    @end
#### 注册方法声明在UIViewController类内
    [self registerForPreviewingWithDelegate:self sourceView:self.view];
    
    
 著作权归作者所有，原文链接:http://www.jianshu.com/p/74fe6cbc542b
