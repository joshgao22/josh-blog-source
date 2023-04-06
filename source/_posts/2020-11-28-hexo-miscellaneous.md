---
title: Hexo-NexT 折腾就完事儿了
tags:
  - 博客
  - Hexo
  - Next
categories:
  - - Blog
    - Hexo
    - Next
abbrlink: b158f1b4
date: 2020-11-28 01:27:30
---


# 瞎折腾/~~美化~~侧边栏

在博客根目录下的 `source/_data` 文件夹中新建 `sidebar.swig` 文件。

## 添加 Tag Cloud

安装 `hexo-tag-cloud` 插件。

``` shell
npm install hexo-tag-cloud --save
```

添加相关代码写入 `sidebar.swig`。不喜欢标题的把 `<h3 class="widget-title">Tag Cloud</h3>` 去掉即可。

``` html source/_data/sidebar.swig
{% if site.tags.length > 1 %}
<script type="text/javascript" charset="utf-8" src="/js/tagcloud.js"></script>
<script type="text/javascript" charset="utf-8" src="/js/tagcanvas.js"></script>
<div class="widget-wrap">
    <h3 class="widget-title">Tag Cloud</h3>
    <div id="myCanvasContainer" class="widget tagcloud" style="font-size:20px;">
        <canvas width="220" height="200" id="resCanvas" style="width=100%">
            {{ list_tags() }}
        </canvas>
    </div>
</div>
{% endif %}
```

<!-- more -->

# 瞎折腾/~~美化~~页面

在博客根目录下的 `source/_data` 文件夹中新建 `variables.styl` 文件。

``` shell
cd hexo
vi source/_data/variables.styl
```

## 页面圆角

在 `variables.styl` 文件里添加圆角半径值

``` stylus source/_data/variables.styl
// 圆角设置
$border-radius-inner     = 0px;     // 内部圆角半径
$border-radius           = 8px;     // 外部圆角半径
```

# 需要修改源代码的操作

## 修改侧边栏最大高度

Sidebar 的高度在 `themes/next/source/js/util.js` 文件中计算出，修改计算公式可调整 Sidebar 的最大高度。

``` javascript themes/next/source/js/util.js - 原 Sidebar 最大高度
/**
 * Init Sidebar & TOC inner dimensions on all pages and for all schemes.
 * Need for Sidebar/TOC inner scrolling if content taller then viewport.
 */
initSidebarDimension: function() {
  // ...
  // Initialize Sidebar & TOC Height.
  var sidebarWrapperHeight = document.body.offsetHeight - sidebarSchemePadding + 'px';
  // ...
},
```

``` javascript themes/next/source/js/util.js - 修改后的 Sidebar 最大高度
initSidebarDimension: function() {
  // ...
  // Initialize Sidebar & TOC Height.
  var sidebarWrapperHeight = document.body.offsetHeight - sidebarSchemePadding -100 + 'px';
  // ...
},
```

- 参考文档：[Hexo Next 博客自定义配置 -> 更改-sidebar-大小](https://yi-yun.github.io/Hexo-Next-Custom/#更改-sidebar-大小https://yi-yun.github.io/Hexo-Next-Custom/#更改-sidebar-大小)

# 重装系统后配置 Hexo-Next

## 安装 Hexo 的支持环境

按照 [Hexo 官方文档](https://hexo.io/zh-cn/docs/index.html)的指导，Hexo 需要 [Node.js](http://nodejs.org/) (Node.js 版本需不低于 10.13，建议使用 Node.js 12.0 及以上版本) 和 [Git](http://git-scm.com/) 方可运行，因此首先需要下载安装 Node.js 和 Git。

### 生成 SSH 添加到 GitHub

使用 Git 之前需要配置用户名和邮箱，需要与 Github 用户名和邮箱一致。

``` shell
$ git config --global user.email "you@example.com"
$ git config --global user.name "Your Name"
```

然后创建 SSH，一路回车。

``` shell
$ ssh-keygen -t rsa -C "youremail"
```

生成的 `id_rsa` 是你这台电脑的私人秘钥，不能给别人看的，`id_rsa.pub` 是公共秘钥，可以随便给别人看。把这个公钥放在 GitHub 上，这样当你链接 GitHub 自己的账户时，它就会根据公钥匹配你的私钥，当能够相互匹配时，才能够顺利的通过 git 上传你的文件到 GitHub 上。

而后在 GitHub 的 setting 中，找到 SSH keys 的设置选项，点击 `New SSH key` 把你的 `id_rsa.pub` 里面的信息复制进去。

在 gitbash 中，查看是否成功

``` shell
$ ssh -T git@github.com
```

## 安装 Hexo

所有必备的应用程序安装完成后，即可使用 `npm` 安装 Hexo。

``` shell
$ npm install -g hexo-cli
```

## 安装 Next 用到的环境

### 安装 Pandoc

若文章中包含使用 [MathJax](https://www.mathjax.org/) 编译的公式，则需要首先安装 [pandoc](https://github.com/jgm/pandoc)，才能编译包含公式的博客页面。

安装完后一定记得**重启**，方可正常使用。
