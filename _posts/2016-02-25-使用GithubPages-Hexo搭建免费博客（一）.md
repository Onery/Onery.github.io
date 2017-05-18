---
layout : post
title: 使用GithubPages-Hexo搭建免费博客_Mac（一）
date: 2016-02-25 15:53:51
tags: Blog
excerpt: "根据hexo搭建博客-步骤1"
comments: true
---

### 前言
最近业务不是很忙，帮同事搭建了一个Blog，我自己的博客用的是Jekyll模板，这里用的是Hexo，具体使用看自己喜好。

### 关于MarkDown
Markdown 是一种用来写作的轻量级「标记语言」，它用简洁的语法代替排版，而不像一般我们用的字处理软件 Word 或 Pages 有大量的排版、字体设置。它使我们专心于码字，用「标记」语法，来代替常见的排版格式。

其详细的介绍，可以看下[Markdown 语法说明 (简体中文版)](http://wowubuntu.com/markdown/#list)

##### 优点
* 专注你的文字内容，排版样式也只是需要键盘即可搞定
* 纯文本，兼容所有的文本编辑器与文字处理软件
* 可读，直观，简介，学习成本低

##### [Markdown使用示例](https://www.zybuluo.com/mdeditor)

##### 书写工具
Mac下有两款优秀的 Markdown 编辑器，[Mou](http://25.io/mou/)和[MacDown](http://macdown.uranusjr.com/)还有[作业部落](https://www.zybuluo.com/mdeditor)等等

![Mou logo](http://25.io/mou/Mou_128.png)
![MacDown logo](http://macdown.uranusjr.com/static/base/img/logo-160.png)
![作业部落 logo](https://www.zybuluo.com/static/img/logo.png)
关于其优缺点，可以移步[这里](http://www.jianshu.com/p/6c157af09e84),至于选择哪款，看个人喜好。
---

### 关于Hexo
Hexo是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。
[Hexo官网](https://hexo.io/zh-cn/)

### 关于GithubPages
在Github里面，每一个项目都拥有它的一个主页，列出项目的源文件，但是对于新手来说，看到那么多的源代码，只会让人感到头晕脑胀，无从下手，他更希望的是，该项目有一个简明易懂的页面，告诉他每一步要怎么去做。

因此，Github就设计了Github Pages这个功能，允许用户自定义项目首页，用来替代默认的源码列表。所以，Github Pages可以被认为是用户编写的、托管在github上的静态网页。

通过上面的介绍，大家对Hexo和Github已经有了大概的了解。
我们的方式就是，利用Markdown进行博客的编写，通过Hexo这个框架解析生成靓丽的静态页面，然后部署到Github上供大家浏览。


## 环境搭建


* 硬件
  此版本为Mac版👽
* 软件
 1. node.js
 2. npm
 3. hexo
 4. github账号
 
### 安装node.js与npm
下载node.js 有多种方法：使用 [Homebrew](http://brew.sh/index_zh-cn.html) 下载 或者直接下载安装包。 建议 node.js 直接下载 安装包，因为使用 brew 有可能失败，会被墙掉。

[node.js下载地址](https://nodejs.org/en/download/) 

![](http://7xr7f9.com1.z0.glb.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-02-26%20%E4%B8%8B%E5%8D%883.37.18.png)

node.js 下载完成后 安装到电脑上就可以了。安装成功后显示出来安装路径，可以看到 安装node.js 的时候 npm 也安装了。

检测安装是否成功 终端输入 -v , 成功则显示版本号

```
$ node -v
v4.3.1
$ npm -v
2.14.12
```

###  安装[Hexo](https://hexo.io/zh-cn/docs/)
文档里给出了详细的安装方法，只需要按其一步步来就好

```
$ sudo npm install -g hexo-cli
```

-v表示全局安装，所以需要使用管理员身份

安装成功，使用hexo 命令的时候，可能会出现以下错误

```
{ [Error: Cannot find module './build/Release/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }    
{ [Error: Cannot find module './build/default/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
{ [Error: Cannot find module './build/Debug/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
```

据说是由于GFW的问题，导致安装Hexo的时候少装了几个库。

查询出的解决方法为，使用以下命令行重新安装

```
npm install hexo –no-optional
```

然而于我而言，并没有什么卵用。这个错误也没有造成任何影响，所以忽略，Go on。
完成后，验证下是否安装成功

```
hexo -v
```

#### Hexo的使用

##### 1. 创建存放博客的文件夹

```
$ mkdir myblog
```

##### 2. 执行以下命令，Hexo会在目标文件夹建立博客所需要的文件

```
hexo init
```

 输出以下信息
 
```
INFO  Cloning hexo-starter to ~/Documents/mytest
Cloning into '/Users/Quncao/Documents/mytest'...
```

成功之后文件夹的样子
![](http://7xr7f9.com1.z0.glb.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-02-26%20%E4%B8%8B%E5%8D%884.13.00.png)

* _config.yml : Hexo和站点的配置文件，里面可以设置博客的名字、标题、作者、链接格式等相关项
* scaffolds : 脚手架，用于存放我们创建文章时的模版
* source : 用于存放我们用markdown编写的博文原文件、其他静态资源文件
* themes : 用于存放主题，里面有我们博客的默认主题landscape

##### 3. 执行以下命令，进行依赖包的安装

```
sudo npm install
```
* node_modules: 关联保存了将会使用到的hexo依赖包

##### 4. 安装相关插件
插件会安装至node_modules文件夹下，如果已经安装好的可以直接忽略

* 安装便于自动部署到Github上的插件

 ```
$ npm install hexo-deployer-git --save
 ```

* 安装atom生成插件，便于感兴趣的小伙伴们订阅

 ```
$ npm install hexo-generator-feed --save
 ```

* 安装博客首页生成插件

 ```
$ npm install hexo-generator-index --save
 ```

* 安装归档生成插件

 ```
$ npm install hexo-generator-archive --save
 ```

* 安装tag生成插件

 ```
$ npm install hexo-generator-tag --save
 ```

* 安装category生成插件

 ```
$ npm install hexo-generator-category --save
 ```

* 安装Sitemap文件生成插件

 ```
$ npm install hexo-generator-sitemap --save
```

* 安装百度Sitemap文件生成插件，因为普通的Sitemap格式不符合百度的要求

 ```
$ npm install hexo-generator-baidu-sitemap --save
 ```
 
未完待续，继续请移步[这里](http://icehulu.com/2016/02/26/%E4%BD%BF%E7%94%A8GithubPages-Hexo%E6%90%AD%E5%BB%BA%E5%85%8D%E8%B4%B9%E5%8D%9A%E5%AE%A2%EF%BC%88%E4%BA%8C%EF%BC%89/)


