---
layout : post
title: 使用GithubPages-Hexo搭建免费博客_Mac（三）
date: 2016-02-26 10:53:51
tags: Blog
excerpt: "根据hexo搭建博客-步骤3"
comments: true
---

## 域名绑定

* 域名解析
域名购买成功后，在解析设置中添加以下解析

![](http://7xr7f9.com1.z0.glb.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-03-03%20%E4%B8%8A%E5%8D%8812.02.04.png)

记录类型为：A
主机记录分别为“@”，“wwww”。其中设置@，可以用xxx.com进行访问。设置www，可以使用www.xxx.com进行访问。
记录值都为 “192.30.252.153”

* 添加CNAME文件

打开自己本地的博客目录，在source文件夹下创建名为CNAME的文本文件。打开，输入自己的域名，格式为“xxx.com”，保存后使用

```
hexo g
hexo d
```
部署到github。

至此，便可使用我们自己的域名进行访问了！

## 图片存储

可以使用[七牛云存储](https://portal.qiniu.com/)

## [Mac上好用的GIF图截屏工具](http://www.cockos.com/licecap/)
