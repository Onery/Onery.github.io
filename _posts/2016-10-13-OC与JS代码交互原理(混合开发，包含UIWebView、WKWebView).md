---
layout: post
title: OC与JS代码交互原理(混合开发，包含UIWebView、WKWebView)
date: 2016-10-13 18:49:50
tags: OC与JS代码交互原理
excerpt: "OC与JS代码交互原理"
comments: true
---

>简单说两句，混合开发的App（Hybrid App）就是在一个App中内嵌一个轻量级的浏览器，一部分原生的功能改为Html 5来开发，这部分功能不仅能够在不升级App的情况下动态更新，而且可以在Android或iOS的App上同时运行，让用户的体验更好又可以节省开发的资源。
混合开发又它的优势，也有其劣势。所以并不是所有的app都是和混合开发，对于一些强交互的app用h5来做的话，会事倍功半，而且体验上面也会大打折扣。所以，公司在选择开发模式的时候，一定要选择适合自己产品的，而不是一味的跟风

>关于第三发框架有很多，这里就不一一列举(我知道的也不多)，在这里我介绍一下交互的底层原理，对于iOS主要分为(UIWebView-JS、WKWebView-JS)，两者的原理有所不同，但是都需要了解的

#UIWebView 与JS交互
##OC调用JS
>这里苹果公司已经为我们封装好了一个方法
>其中script里面放的就是纯JS代码，而我们只需要用UIWebView的
```
stringByEvaluatingJavaScriptFromString:(NSString *)script
```
>对象方法就可以直接执行字符串所存储的JS代码，当然，这个执行，是在整个页面加载完以后才会去执行的。
```
NSString *js = @"var div = document.getElementById('div1'); div.style.backgroundColor = 'yellow';div.addEventListener('click',divClick);function divClick(){alert('123456789')}";
    /*加载js脚本*/
 [_webView stringByEvaluatingJavaScriptFromString:js];
```

##JS调用OC
>这需要UIWebView的一个代理方法来辅助操作，当JS需要调用OC 的一个方法的时候会主动发起一个网络请求

```
window.location.href = "openimagepicker://"
```
>只要JS掉用和href这个方法，就会走UIWebView 的代理方法：

```
(BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType
```
>并且将 "openimagepicker://"当做request 传进来，也就是说，我们需要在这个代理方法里来拦截这个request，看是否是OC来处理的，比如说我当前发送的request的意思是"打开相册"(事先约定好)，我通过webview 的代理方法拦截到了这个请求，那么我就会主动的掉起系统原生相册，代码如下:

```
- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType
{
    NSLog(@"%@",request.URL.absoluteString);
    //获取webView发送的请求
    NSString *urlString = request.URL.absoluteString;
    /**
     *  打开相册
     */
    if ([urlString isEqualToString:@"openimagepicker://"])
    {
        [self openImagePicker];
        return NO;
    }
    return YES;
}
- (void)openImagePicker
{
    UIImagePickerController *imagePickerController = [[UIImagePickerController alloc] init];
    [self presentViewController:imagePickerController animated:YES completion:nil];
}
```
>return  NO是为了不让webview 来处理这次网络请求，因为这并不是一次真正的网络请求

#WKWebView与JS交互
##JS调用OC
>相对于UIWebView，WKWebView与JS进行交互时则要麻烦很多，大体上分为3个步骤:
1.首先我们在初始化WKWebView的时候 需要将一个配置项同事给webView，这里面的配置项，使我们来注册handler的(一会再解释这是干啥的)
2.注册完后，网页跑了起来，JS需要通过window.webkit.messageHandlers.<name>.postMessage(<messageBody>)调用的触发webView的代理方法
3.Native(原生)通过代理方法，来处理响应的事件

###需要的类(OC)
>WKWebViewConfiguration  这个类就是webView初始化时候的配置项，它会有一个方法，让我们来注册handler
```
- (void)addScriptMessageHandler:(id <WKScriptMessageHandler>)scriptMessageHandler name:(NSString *)name;
```
这个方法就是来注册handler用的

完整代码块
```
NSString *path = [[NSBundle mainBundle] pathForResource:@"index.html" ofType:nil];
    //2.读取文件中的内容
NSString *string = [NSString stringWithContentsOfURL:[NSURL fileURLWithPath:path] encoding:NSUTF8StringEncoding error:nil];
WKWebViewConfiguration *configuration = [[WKWebViewConfiguration alloc] init];
    //注册，方便js调用oc代码
[configuration.userContentController addScriptMessageHandler:self name:@"OpenImagePicker"];
[configuration.userContentController addScriptMessageHandler:self name:@"SystemVersion"];
WKWebView *webView = [[WKWebView alloc] initWithFrame:self.view.bounds configuration:configuration];
    //加载指定的HTML字符串
[webView loadHTMLString:string baseURL:nil];
webView.UIDelegate = self;
[self.view addSubview:webView];
```

>假如JS主动调起了OC，webView 的这个代理方法会立即执行,我们可以在这里处理JS需要Native做的事情

```
//JS通过window.webkit.messageHandlers.<name>.postMessage(<messageBody>)调用的触发此代理方法
- (void)userContentController:(WKUserContentController *)userContentController didReceiveScriptMessage:(WKScriptMessage *)message
{
    //打开相册
    if ([message.name isEqualToString:@"OpenImagePicker"])
    {
        //js的传过来的数据
        NSLog(@"%@",message.body);
        UIImagePickerController *picker = [[UIImagePickerController alloc] init];
        [self presentViewController:picker animated:YES completion:nil];
        
    }
    //获取系统版本
    if ([message.name isEqualToString:@"SystemVersion"])
    {...}
}
```

>那么问题来了？JS怎么来主动掉起Native 来这行上面这个代理方法呢，还记得刚才注册的handler么？那是注册在webView上的，就相当于OC跟JS事先商量好，有哪些字段会调起我的代理方法，其他字段是不可以的！
JS 的代码如下：

```
window.webkit.messageHandlers.OpenImagePicker.postMessage("123456789")
```
其中，"OpenImagePicker"并不是JS 的方法名，而是我们之前在webView里注册 的handler，"postMessage"是JS带给OC 的一些信息，如果"OpenImagePicker"我们事先没有注册到webView里，当JS执行这句代码时，是不会走webView 的代理方法的。

##OC调用JS
>OC调用JS相对要简单一些，有两种，其中一种跟UIWebView 的方法一样，都是系统自带的方法，下面贴代码

```
[webView evaluateJavaScript:@"function sum(a,b){return a + b};sum(1,2)" completionHandler:^(id result, NSError * _Nullable error) {
        NSLog(@"%@",result);
    }];
```
自己去看log出来的结果是啥
>还有第二种，就是在webView初始化的时候，直接注入JS代码，放在JS代码的最后面执行，贴代码

```
 //初始化了script
    WKUserScript *script = [[WKUserScript alloc] initWithSource:@"buttonClick()" injectionTime:WKUserScriptInjectionTimeAtDocumentEnd forMainFrameOnly:YES];
    WKWebViewConfiguration *configuration = [[WKWebViewConfiguration alloc] init];
    //执行js语言的
    [configuration.userContentController addUserScript:script];
    
    WKWebView *webView = [[WKWebView alloc] initWithFrame:self.view.bounds configuration:configuration];
    //加载指定的HTML字符串
    [webView loadHTMLString:string baseURL:nil];
    webView.UIDelegate = self;
    //加载指定路径下的文件
    [self.view addSubview:webView];
```
多余的代码我没贴，这里还是很容易懂的。

###总结：哔哔这么多，就是让大家知道，我是谁？我在哪？在干吗？原理还是要略知一二的，大多数用的都是别人的框架，用的时候很少考虑原理，当业务需求有变化的时候，需要改底层框架！就会感叹一句：卧槽！我是谁？我在哪？在干吗？
                                                                                                         阿里嘎豆！



