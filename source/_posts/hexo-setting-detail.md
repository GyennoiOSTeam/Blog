---
title: Hexo 和 Next 主题构建 GitHub 的 Blog
author: lmsss
date: 2018-10-10 16:47:10
copyright: true
tags:
---
摘要：
    本文采用Hexo 与 Next 来构建GitHub pages 的操作
    
<!-- more -->

#### Node.js 的安装

~~~ objc
$ brew install node
~~~

#### Hexo 的安装

Hexo 是高效的静态站点生成框架，她基于 Node.js。 通过 Hexo 你可以轻松地使用 Markdown 编写文章，除了 Markdown 本身的语法之外，还可以使用 Hexo 提供的 标签插件 来快速的插入特定形式的内容。在这篇文章中，假定你已经成功安装了 Hexo，并使用 Hexo 提供的命令创建了一个站点。

~~~ objc
 npm install -g hexo-cli //全局安装hexo的客户端
~~~

如果不成功加上 sudo

如果执行成功使用 hexo -v 查看是否安装成功

##### Hexo 的初始化

~~~ objc
    hexo init // 这里可以针对具体的文件夹进行初始化的操作
    npm install // 安装相对应的依赖包
~~~

##### Hexo 的框架简介

当hexo init 执行完成后，会在当前的文件夹中增加框架对用的目录结构

~~~ objc
.
├── .deploy_git  // 执行hexo deploy 命令部署到GitHub 上的内容目录
├── public   // 执行 hexo generate 命令，输出的静态网页目录
├── scaffolds // layout 模板的文件目录，其中的md文件是可以添加编辑的
├── scripts // 扩展脚本目录
├── source  // 文章源码目录
|   ├── _drafts // 草稿文章
|   └── _posts  // 发布文章
├── themes  // 主题文件目录
├── _config.yml // 全局配置文件，主要的站点设置都是在这里完成的
└── package.json // 应用程序中的数据 类似于一些系统信息
~~~

##### Hexo 命令简介

~~~ objc

hexo generate // 简写 hexo g 生成静态文件，会在当前目录下生成一个新的public 的文件夹

hexo server // 简写 hexo s 启动本地服务器，通过http://localhost:4000进行访问

hexo deploy // 简写 hexo d 部署远程，在_config.yml中配置

hexo new post-name // 简写 hexo n post-name 新建文章

hexo clean  // clean本地项目，防止缓存


~~~

组合命令：

~~~ objc
hexo s -g //生成和预览
hexo d -g //生成和部署
~~~

#### Hexo 配置

##### 语言 时区

~~~ objc

title: GyennoiOSTeam  // 标题
subtitle:  // 子标题
description: just do it // 描述信息
keywords: iOS // 关键字
author: GYENNO // 作者
language: zh-Hans // 语言
timezone: Asia/Shanghai //时区

~~~

##### hexo 到 GitHub

首先需要明确：

> 每一个github的账号都有一个GitHub的pages
> 一个账号只能创建一个reposeitory来存放GitHub pages
>  仓库的名字必须是username/username.github.io 这是固定的命名约定
>  然后通过https://username.github.io来进行访问
>  GitHub 创建的个人主页内容是在master 分支下的

~~~ objc

deploy:  // 部署
type: git  //部署的平台
repo: git@github.com:githubname/githubname.github.io.git // git 地址
branch: master // 分支

~~~

这里主要是针对依赖的地方进行一些配置就能够实现了

安装一个扩展：

~~~ objc

npm install hexo-deployer-git --save

~~~

##### hexo 主题设置

在 Hexo 中有两份主要的配置文件，其名称都是 _config.yml。 其中，一份位于站点根目录下，主要包含 Hexo 本身的配置；另一份位于主题目录下，这份配置由主题作者提供，主要用于配置主题相关的选项。

这里使用的是next 的主题

~~~ objc

$ cd your-hexo-site
$ git clone https://github.com/iissnan/hexo-theme-next themes/next

~~~

然后在 _config.yml 启用主题

~~~ objc
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: next
~~~

这样就可以选择主题的操作

##### 验证主题

~~~ objc
hexo s 
~~~

此时即可使用浏览器访问 http://localhost:4000，检查站点是否正确运行。


#### Next 主题

##### 主题结构

~~~ objc
├── .github            #git信息
├── languages          #多语言
|   ├── default.yml    #默认语言
|   └── zh-Hans.yml      #简体中文
|   └── zh-tw.yml      #繁体中文
├── layout             #布局，根目录下的*.ejs文件是对主页，分页，存档等的控制
|   ├── _custom        #可以自己修改的模板，覆盖原有模板
|   |   ├── _header.swig    #头部样式
|   |   ├── _sidebar.swig   #侧边栏样式
|   ├── _macro        #可以自己修改的模板，覆盖原有模板
|   |   ├── post.swig    #文章模板
|   |   ├── reward.swig    #打赏模板
|   |   ├── sidebar.swig   #侧边栏模板
|   ├── _partial       #局部的布局
|   |   ├── head       #头部模板
|   |   ├── search     #搜索模板
|   |   ├── share      #分享模板
|   ├── _script        #局部的布局
|   ├── _third-party   #第三方模板
|   ├── _layout.swig   #主页面模板
|   ├── index.swig     #主页面模板
|   ├── page           #页面模板
|   └── tag.swig       #tag模板
├── scripts            #script源码
|   ├── tags           #tags的script源码
|   ├── marge.js       #页面模板
├── source             #源码
|   ├── css            #css源码
|   |   ├── _common    #*.styl基础css
|   |   ├── _custom    #*.styl局部css
|   |   └── _mixins    #mixins的css
|   ├── fonts          #字体
|   ├── images         #图片
|   ├── uploads        #添加的文件
|   └── js             #javascript源代码
├── _config.yml        #主题配置文件
└── README.md          #用GitHub的都知道

~~~

##### 选定Scheme

目前Next 支持三种 Scheme 

* Muse - 默认的Scheme 
* Mist-Muse 紧凑版本，整洁有序的单栏外观
* Pisces - 双栏Scheme 

Scheme 的切换可以通过** 主题配置文件 ** 中的Scheme 来进行配置：

~~~ objc

# Schemes
#scheme: Muse
scheme: Mist
#scheme: Pisces
#scheme: Gemini

~~~

如果要使用只要加上注释就可以了  # 

##### 设置语言

需要在站点的配置文件中将 language 设置成你所需要的语言

~~~ objc
language: zh-Hans
~~~

##### 修改底部带#号的标签

具体的办法：
~~~ objc
修改模板/themes/next/layout/_macro/post.swig，搜索 rel="tag">#，将 # 换成 <i class="fa fa-tag"></i>
~~~

##### 在每篇文章末尾统一添加“结束”标记

在路径 \themes\next\layout\_macro 中新建一个 passage-end-tag.swig 文件，并添加内容

~~~ objc
<div>
{% if not is_index %}
<div style="text-align:center;color: #ccc;font-size:14px;">-------------本文结束<i class="fa fa-paw"></i>感谢您的阅读-------------</div>
{% endif %}
</div>
~~~

然后需要将这个文件添加到 '\themes\next\layout\_macro\post.swig' 文件中，在post-body 之后，post-footer 之前添加如下部分

~~~ objc
<div>
{% if not is_index %}
{% include 'passage-end-tag.swig' %}
{% endif %}
</div>
~~~

然后在主题配置文件'_config.yml' 的末尾添加

~~~ objc
# 文章末尾添加“本文结束”标记
passage_end_tag:
enabled: true
~~~

这些完成以后，就可以 'hexo s'预览下

##### 主页文章添加阴影效果

打开\themes\next\source\css\_custom\custom.styl,向里面加入：

~~~ objc
.post {
 margin-top: 60px;
 margin-bottom: 60px;
 padding: 25px;
 -webkit-box-shadow: 0 0 5px rgba(202, 203, 203, .5);
 -moz-box-shadow: 0 0 5px rgba(202, 203, 204, .5);
}
~~~



##### 修改文章的字体的大小

~~~ objc
global:
external: true
family: Lato
size: 16
~~~
这里可以修改字体的大小操作

##### 增加文章底部版权信息

首先要再主题文件中设置
~~~ objc
post_copyright:
enable: true
license: CC BY-NC-SA 3.0
license_url: https://creativecommons.org/licenses/by-nc-sa/3.0/
~~~

要将false 该为true

然后需要对 themes/next/layout/_macro/post-copyright.swig 文件进行编辑操作

~~~ objc

{% if page.copyright %}
<ul class="post-copyright">
<li class="post-copyright-author">
<strong>本文标题:</strong>
<a href="{{ url_for(page.path) }}">{{ page.title }}</a>
</li>
<li class="post-copyright-author">
<strong>{{ __('post.copyright.author') + __('symbol.colon') }}</strong>
{{ config.author }}
</li>
<li class="post-copyright-author">
<strong>发布时间:</strong>
{{ page.date.format("YYYY年MM月DD日 - HH:MM") }}
</li>
<li class="post-copyright-author">
<strong>最后更新:</strong>
{{ page.updated.format("YYYY年MM月DD日 - HH:MM") }}
</li>
<li class="post-copyright-link">
<strong>{{ __('post.copyright.link') + __('symbol.colon') }}</strong>
<a href="{{ post.permalink }}" title="{{ post.title }}">{{ post.permalink }}</a>
</li>
<li class="post-copyright-license">
<strong>{{ __('post.copyright.license_title') + __('symbol.colon') }} </strong>
{{ __('post.copyright.license_content', theme.post_copyright.license_url, theme.post_copyright.license) }}
</li>
</ul>
{% endif %}


~~~

这样操作后需要在你的文章头部 添加一个copyright: true 的操作，这样才可以进行显示的操作



