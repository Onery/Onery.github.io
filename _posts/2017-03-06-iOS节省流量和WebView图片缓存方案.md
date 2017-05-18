---
layout: post
title: iOS节省流量和WebView图片缓存方案
date: 2017-03-06 13:49:50
tags: iOS节省流量和WebView图片缓存方案
excerpt: "在上一篇文章中说过我们的项目使用了HybridKit，感兴趣的可以去看下《OC与JS代码交互原理》。项目上线之后有用户反馈，在使用过程中流量消耗特别快，so为了满足上帝的需求，有了这边文章。"
comments: true
---

在上一篇文章中说过我们的项目使用了HybridKit，感兴趣的可以去看下[《OC与JS代码交互原理》](http://oneryhandsome.com/OC%E4%B8%8EJS%E4%BB%A3%E7%A0%81%E4%BA%A4%E4%BA%92%E5%8E%9F%E7%90%86(%E6%B7%B7%E5%90%88%E5%BC%80%E5%8F%91-%E5%8C%85%E5%90%ABUIWebView-WKWebView)/)。项目上线之后有用户反馈，在使用过程中流量消耗特别快，so为了满足上帝的需求，有了这边文章。


## 流量消耗过快原因
- **接口返回中包含无用的数据**
- **无用的请求(多次同时请求同一个接口)** 
- **图片和数据未作缓存处理**
- **图片过大**
- **App和后台交互的协议种类**
- **暂时只了解到这么多，欢迎补充**

## 解决方案

基于我们项目的情况，我们目前只能从图片解决。因为项目中部分界面使用Html 5展示的，无法使用SDWebImage进行缓存，进而会多次请求图片造成流量浪费。在之前做过图片的格式转换和裁剪,但是最后都打不到预期效果。直到查阅到[***WebP***](https://zh.wikipedia.org/wiki/WebP),又很多公司都在用WebP格式，比如淘宝、Facebook、腾讯等。[***WebP***](https://zh.wikipedia.org/wiki/WebP)的优点不做赘述，自己看下[维基百科](https://zh.wikipedia.org/wiki/WebP)的介绍。

1. 对图片使用WebP格式
2. 截取Html，获取图片地址，本地使用SDWebImage下载图片，并缓存图片，下载后的图片和原地址一一替换。

## 引用WebP
1. Pods
```
OnerydeMacBook-Pro:onery-Blog onery$ pod 'SDWebImage/WebP'
```

2. 手动导入
	1. 首先下载[WebP](https://github.com/seanooi/iOS-WebP)
	2. 将SDWebImage和下载好的WebP导入工程
	3. 此时SDWebImage尚不支持WebP，还需要做一点操作:Build Settings --> Preprocessor,在Preprocessor中添加***SD_WEBP=1***

##截取Html中的图片
###NSURLCache
NSURLCache提供的是内存以及磁盘的综合缓存机制，`NSURLCache`只会对你的`get`请求进行缓存，默认是512kb的内存缓存空间和10MB的磁盘缓存空间，可以通过代码查看和设置大小：

- **查看磁盘和内存缓存空间**

```
NSLog(@"磁盘缓存空间大小：%lu",(unsigned long)[[NSURLCache sharedURLCache] diskCapacity]);
//log:磁盘缓存空间大小：10000000

NSLog(@"内存缓存空间大小：%lu",(unsigned long)[[NSURLCache sharedURLCache] memoryCapacity]);
//log:内存缓存空间大小：512000
```

- **设置磁盘和内存缓存空间**

*设置存储空间需要在`- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
`方法中设置*
```
NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                     diskCapacity:20 * 1024 * 1024
                                                         diskPath:nil];                                                         
                                                         
[NSURLCache setSharedURLCache:URLCache];
```
###NSURLProtocol
NSURLProtocol能够让你去重新定义苹果的URL加载系统 (URL Loading System)的行为，URL Loading System里有许多类用于处理URL请求，比如NSURL，NSURLRequest，NSURLConnection和NSURLSession等，**最重要的是NSURLProtocol可以拦截到WebView发出的请求**，当URL Loading System使用NSURLRequest去获取资源的时候，会创建一个NSURLProtocol子类的实例，不应该直接实例化一个NSURLProtocol，NSURLProtocol看起来像是一个协议，但其实这是一个类，而且必须使用该类的`子类`，并且需要在`APPDelegate`中被注册。
通过NSURLProtocol我们可以作如下操作：

- **重定向网络请求(更改请求指向到指定的地址)**
- **不使用网络请求，使用本地的缓存**
- **自定义请求返回结果**
- **设置全局网络请求**

子类化NSURLProtocol
```
@interface YJURLProtocol : NSURLProtocol
@end
```
在`- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions`中注册，注册成功后，才能够处理所有URL Loading system的请求。
```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.
    [NSURLProtocol registerClass:[YJURLProtocol class]];
    return YES;
}
```
实现代理方法
```
+ (BOOL)canInitWithRequest:(NSURLRequest *)request {
	//如果不是UPYUN的地址，就不做拦截，有系统自动处理
	if ([[request.URL absoluteString] rangeOfString:@"upaiyun"].location == NSNotFound) {
        return NO;
    }

    //在我们项目中只处理http和https请求
    NSString *scheme = [[request URL] scheme];
    if ( ([scheme caseInsensitiveCompare:@"http"] == NSOrderedSame ||
     [scheme caseInsensitiveCompare:@"https"] == NSOrderedSame))
    {
        //看看是否已经处理过了，防止无限次处理，耗费资源
        if ([NSURLProtocol propertyForKey:URLProtocolHandledKey inRequest:request]) {
            return NO;
        }

	 NSString *agent = [request valueForHTTPHeaderField:@"User-Agent"];
        // 只过滤UIWebview里边的加载图片请求
        if ([agent rangeOfString:@"AppleWebKit"].location != NSNotFound &&
        	[request.URL isImageURL]) {
            return YES;
        }

    }
    return NO;
}
```

```
//用来判断链接时候是图片地址
- (BOOL)isImageURL {
    NSArray *extensions = @[@"jpg", @"jpeg", @"png",@"webp"];
    for (NSString *extension in extensions) {
        if ([self.absoluteString.lowercaseString rangeOfString:extension options:NSCaseInsensitiveSearch].location != NSNotFound){
            return YES;
        }
    }
    return NO;
}
```

调用NSURLProtocol的`startLoading`方法
```
- (void)startLoading {
    NSMutableURLRequest *mutableReqeust = [[self request] mutableCopy];
    //标示改request已经处理过了，防止无限循环
    [NSURLProtocol setProperty:@YES forKey:URLProtocolHandledKey inRequest:mutableReqeust];
    NSString *URLString = [self.request.URL absoluteString];
    
    // 重定义请求地址
    if ([URLString rangeOfString:@"format"].location == NSNotFound) {
        URLString = [[BFWebImageHelper imageStringToURL:URLString width:0 height:0] absoluteString];
//        if ([URLString rangeOfString:@"!"].location == NSNotFound) {
//            // 为图请求地址加载web格式
//            URLString = [NSString stringWithFormat:@"%@!/format/webp",URLString];
//        }
//        // 为图请求地址加载web格式
//        URLString = [NSString stringWithFormat:@"%@/format/webp",URLString];
    }
    else if ([URLString rangeOfString:@"format/jpg"].location != NSNotFound) {
        URLString = [[BFWebImageHelper imageStringToURL:URLString width:0 height:0] absoluteString];
//        // 将format为jpg改为webp格式
//        URLString = [URLString stringByReplacingOccurrencesOfString:@"format/jpg" withString:@"format/webp"];
    }
    else {
        self.connection = [NSURLConnection connectionWithRequest:mutableReqeust delegate:self];
        return;
    }
    
    NSURL *url = [NSURL URLWithString:URLString];
    
    [[SDWebImageManager sharedManager] downloadImageWithURL:url
     						options:SDWebImageAllowInvalidSSLCertificates 
					     progress:nil 
						 completed:^(UIImage *image, NSError *error, SDImageCacheType cacheType, BOOL finished, NSURL *imageURL) {
								__strong __typeof(weakSelf)strongSelf = weakSelf;
								[strongSelf handleLoadImgWithURL:imageURL image:imageURL];
								}];
}

- (void)handleLoadImgWithURL:(NSURL *)imgUrl image:(UIImage *)image{
        NSData *data;
        // 是否以png结尾
        if ([imgUrl.absoluteString.lowercaseString hasSuffix:@".png"]) {
            data = UIImagePNGRepresentation(image);
        } else {
            data = UIImageJPEGRepresentation(image, 1);
        }
        if (!self.client) {
            return ;
        }
        [self.client URLProtocol:self didLoadData:data];
        [self.client URLProtocolDidFinishLoading:self];
}
```

```
- (void)connection:(NSURLConnection *)connection didReceiveResponse:(NSURLResponse *)response {
    [self.client URLProtocol:self didReceiveResponse:response cacheStoragePolicy:NSURLCacheStorageNotAllowed];
}

- (void)connection:(NSURLConnection *)connection didReceiveData:(NSData *)data {
    [self.client URLProtocol:self didLoadData:data];
}

- (void)connectionDidFinishLoading:(NSURLConnection *)connection {
    [self.client URLProtocolDidFinishLoading:self];
}

- (void)connection:(NSURLConnection *)connection didFailWithError:(NSError *)error {
    [self.client URLProtocol:self didFailWithError:error];
}
```

采用如上方案后，Webp为我们节省了至少40%流量，NSURLProtocol在WebP的基础又节省了10%左右，同是呈献给用户的图片与未优化前没有肉眼可以发现的区别，总的来说本次优化比较成功。
以上是我们项目的经验，如有不对欢迎指正~。


