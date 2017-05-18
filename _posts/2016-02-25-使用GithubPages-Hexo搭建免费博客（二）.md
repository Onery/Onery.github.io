---
layout : post
title: 使用GithubPages-Hexo搭建免费博客_Mac（二）
date: 2016-02-25 15:53:51
tags: Blog
excerpt: "根据hexo搭建博客-步骤2"
comments: true
---

上篇讲到Hexo的安装，本篇继续，先来说说Hexo安装后的文件结构与功能。

## HEXO博客配置

#### 1. Hexo设置

打开根目录下的_config.yml文件
 
##### 1.1 站点配置

```
1 # Site
2 title: myblog   # 网站标题
3 subtitle:      # 网站子标题
4 description: 这是一个利用Hexo搭建的博客    # 网站描述
5 author:  author   # 网站作者，也就是您的名字
6 language: zh-cn   # 网站使用的语言        
7 timezone:         # 网站时区。Hexo 预设使用您电脑的时区。
```

##### 1.2 网址配置
```
1 # URL
2 url: http://xiaoxuetu.github.io         # 博客网址
3 root: /                                 # 网站根目录
4 permalink: :year/:month/:day/:title/    # 文章的永久链接格式   :year/:month/:day/:title/
5 permalink_defaults:                     # 永久链接中各部分的默认值
```
*注意！ 如果你的网站存放在子目录中，例如 http://xiaoxuetu.github.io/blog, 则将url设为http://xiaoxuetu.github.io/blog， 并且把 root 设为/blog/。*

##### 1.3 目录配置

一般直接取默认值不用修改。

```
1 # Directory
2 source_dir: source         # 资源文件夹，这个文件夹用来存放内容，例如我们用markdown编写的博文
3 public_dir: public         # 公共文件夹，这个文件夹用于存放生成的静态博客文件。
4 tag_dir: tags              # 标签文件夹
5 archive_dir: archives      # 归档文件夹
6 category_dir: categories   # 分类文件夹
7 code_dir: downloads/code   # Include code 文件夹
8 i18n_dir: :lang            # 国际化（i18n）文件夹
9 skip_render:               # 跳过指定文件的渲染，您可使用 glob 来配置路径。
```
##### 1.4 文章配置

一般直接取默认值不用修改。

```
1 # Writing
 2 new_post_name: :title.md    # 新文章的文件名称
 3 default_layout: post        # 预设布局
 4 titlecase: false            # 把标题转换为 titlecase（titlecase指的是将每个单词首字母转换成大写）
 5 external_link: true         # 在新标签中打开链接
 6 filename_case: 0            # 把文件名称转换为 (1) 小写或 (2) 大写, 0表示不变
 7 render_drafts: false        # 显示草稿
 8 post_asset_folder: false    # 启动 Asset 文件夹
 9 relative_link: false        # 把链接改为与根目录的相对位址
10 future: true                # 显示未来的文章
11 highlight:                  # 代码块的设置
12   enable: true              
13   line_number: true         # 是否显示行号
14   auto_detect: true         # 是否自动监测
15   tab_replace:              # 将 tab 替换成其他字符串
```

##### 1.5 分类和标签配置

一般直接取默认值不用修改。

```
1 # Category & Tag
2 default_category: uncategorized    # 默认分类, uncategorized表示未分类
3 category_map:                      # 分类别名
4 tag_map:                           # 标签别名
```

##### 1.6 日期 以及 时间格式 配置

```
1 date_format: YYYY-MM-DD           # 日期格式
2 time_format: HH:mm:ss             # 时间格式
```

##### 1.7 分页配置

```
1 # Pagination
2 per_page: 10                      # 每页显示的文章量，如果设置值为0，则表示禁止分野
3 pagination_dir: page              # 分页目录
```
##### 1.8 主题配置
从这里开始，都是属于Hexo拓展插件的配置了。以下代码，主要是主题配置。更详细的主题替换，看第二节

```
1 # Extensions
2 theme: landscape    # 主题设置，默认是 landscape
```
##### 1.9 部署配置

这里主要用于将博客部署到github上，详细教程在后面。

```
1 # Deployment
2 deploy:
3   type:     # 设置发布类型，如git，rsync
```
---
#### 主题设置

Hexo为我们提供了很多的主题供我们选择，网页在[这里](http://hexo.io/themes/) 我们可以自由选择自己喜欢的主题来进行设置。

[github主题列表地址](https://github.com/hexojs/hexo/wiki/Themes)

修改方法，就是将相应的主题clone到博客文件夹下的themes文件夹下，然后将上面1.8中的主题名字设置为相应的修改的名字。
我用的是这个：[hexo-theme-hueman](https://github.com/ppoffice/hexo-theme-hueman)

```
# Extensions
## Themes: https://hexo.io/themes/
theme: hexo-theme-hueman
```

想要看到修改后的效果，可以在终端中继续执行以下命令。

```
hexo clean    
hexo generate
```

以上命令完成后，可以执行

```
hexo s
```
> hexo s 等同于 hexo server , s 其实就是 server的缩写

执行成功后，控制台将会输出

```
INFO  Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop.
```

用浏览器打开此站点，就可以看到我们用Hexo生成的网页。

##### 新建博文

cd到博客文件的根目录下，使用以下命令，新建博客

```
hexo new "title"
```

格式是： hexo new ｛文章名｝
命令执行成功后，你就会发现在 source/_posts 目录下多了一个文件 title.md 。

在文件夹下使用之前我们下载的markdown编辑器，打开这个文件，会看到以下内容

```
 title: title
 date: 
 tags:
 ---
```

含义是：

* title : 文章的标题
* date : 该文章的创建时间
* tags : 该文章的标记tag

依据不同的主题，多标签的语法格式为

```
tags: 
    - 标签1
    - 标签2
```

或者

```
tags: [标签1,标签2]
```

你可以添加你所需要的变量，如果觉得每次生成后更改很麻烦，可以在模板文件夹scaffolds下找到post.md文件，编辑它的Front-matter为你想要的变量参数，默认新建博文使用的post模板。

##### 创建自己制定模板

如果你想使用其他模板，你可以使用下列命令来创建指定模板的博文：

```
hexo new scaffold "title"
```
请确保使用的模板scaffold在scaffolds存在。

##### 文章摘要

这个功能很实用，可以让你在首页文章目录不必显示全部的文章内容，只显示指定摘要。

```
以上是摘要
<!--more-->
以下是全文
```

---

使用markdown编辑好博文后，就可以执行

```
hexo generate
```
此命令主要用于部署网页的静态文件，每次修改后都应该首先执行此命令，来重新部署

然后继续执行“hexo s”启动服务。

至此，Hexo的网页部署全部完成，之后就是部署到github上了。

---

## 部署到github

##### 1.注册账号

如果有github账号的，可以忽略，没有请移步[github](www.github.com)注册账号

##### 2.创建与你的github用户名对应的仓库

仓库名字必须为“username.github.io”， 其中“username”为你的用户名

![](http://7xr7f9.com1.z0.glb.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-03-02%20%E4%B8%8B%E5%8D%8810.38.37.png)

点击右上角的加号，创建new repository，然后输入“username.github.io”，点击创建。

##### 3.创建Github Pages

repository创建完成后进入以下界面

![](http://7xr7f9.com1.z0.glb.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-03-02%20%E4%B8%8B%E5%8D%8810.43.20.png)

点击最右边的Settings

进入后找到创建Github Pages的地方

![](http://7xr7f9.com1.z0.glb.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-03-02%20%E4%B8%8B%E5%8D%8810.45.28.png)

点击 Launch automatic page generator
最后生成主页：

![](http://7xr7f9.com1.z0.glb.clouddn.com/github_pages_themes.png)

之后我们就可以直接访问“username.github.io”，去访问我们的博客了。

##### 4.配置git

由于mac自带git，所以直接打开终端进行设置

设置用户名密码

```
git config --global user.email "你的github邮箱"
git config --global user.name "你的github用户名"
```

生成密钥

```
ssh-keygen -t rsa -C "你的邮箱"
```

回车之后，打开Finder,前往文件夹[~/.ssh]中会看到生成的两个文件
> id_rsa    id_rsa.pub

其中id_rsa是私钥，id_rsa.pub是龚玥

然后执行以下命令，添加生成的key

```
ssh-add id_rsa
```

然后将id_rsa.pub中的内容复制下来，在我们github的主页中，点击个人的账户，找到settings-->SSH keys，将我们复制的内容，添加在这里。title随便取个名字就好。

添加成功之后，可以在终端测试

```
ssh -T git@github.com
```

点击回车后输出内容中，最后一句话为

```
You've successfully authenticated, but github does not provide shell access.
```

至此，验证成功。

##### 5.最后一步，将我们用Hexo生成的网页部署到github

在我们的博客根目录下，打开站点配置文件**_config.yml**

```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy: 
  type: git
  repository: https://github.com/username/username.github.io.git
  branch: master
```
其中“username”为你github的用户名。

修改完保存后，就可以打开终端，cd到博客根目录，执行

```
hexo generate #生成静态网页
hexo deploy #部署到github
```

之后输出以下信息

```
INFO Deploy done: git
```

说明我们的博客已经部署成功，github pages可能会存在延时，过几分钟后，就可以通过username.github.io去访问我们的博客了！

---

*本篇主要讲述了使用Hexo进行博客的搭建与配置，以及如何部署到github上，至此我们所有创建博客的工作已经完成。之后便是自己博客的编写与经营了，一些更深入的配置移步[第三篇](http://icehulu.com/2016/03/02/使用GithubPages-Hexo搭建免费博客（三）/)吧*