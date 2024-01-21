---
title: Hexo-NexT 折腾就完事儿了
tags:
  - 博客
  - Hexo
  - Next
  - 不蒜子
  - Tag Cloud
categories:
  - - Blog
    - Hexo
    - Next
abbrlink: b158f1b4
date: 2020-11-28 01:27:30
---

# 主题安装

NexT 官方提供了两种安装主题的方式，一种是使用将主题作为 node.js 的 Package，直接使用 `npm install` 命令进行安装，另一种是将主题 `git clone` 到 `<path-to-blog>/theme.next` 目录下。

这里更推荐将 NexT 主题作为 node.js 的 Package 进行安装，可以让整个博客文件夹更加紧凑，也方便使用 `npm` 管理版本。安装最新 Release 版 NexT 主题的命令如下：

``` bash
npm install hexo-theme-next@latest
```

安装完成后在 Hexo 的配置中将主题切换为 `next` 即可，即

``` diff _config.yml
- theme: landscape
+ theme: next
```

<!-- more -->

# 环境依赖

> 更新于 2023 年 4 月 15 日

|         Package 名称         |   版本   |
| :--------------------------: | :------: |
|            `hexo`            | `6.3.0`  |
|       `hexo-abbrlink`        | `2.2.1`  |
|     `hexo-deployer-git`      | `4.0.0`  |
| `hexo-filter-github-emojis`  | `3.0.5`  |
|   `hexo-generator-archive`   | `2.0.0`  |
|  `hexo-generator-category`   | `2.0.0`  |
|    `hexo-generator-index`    | `3.0.0`  |
|  `hexo-generator-searchdb`   | `1.4.1`  |
|   `hexo-generator-sitemap`   | `3.0.1`  |
|     `hexo-generator-tag`     | `2.0.0`  |
|     `hexo-helper-live2d`     | `3.1.1`  |
|         `hexo-neat`          | `1.0.9`  |
| `hexo-related-popular-posts` | `5.0.1`  |
|     `hexo-related-posts`     | `1.5.1`  |
|     `hexo-renderer-ejs`      | `2.0.0`  |
|    `hexo-renderer-pandoc`    | `0.3.1`  |
|    `hexo-renderer-stylus`    | `2.1.0`  |
|        `hexo-server`         | `3.0.0`  |
|       `hexo-tag-cloud`       | `2.1.*`  |
|    `hexo-theme-landscape`    | `0.0.3`  |
|      `hexo-theme-next`       | `8.15.1` |
|     `hexo-word-counter`      | `0.1.0`  |

# 侧边栏

## 新建侧边栏配置文件

> 由于 NexT 8.0.0 版本后将 `.swig` 模板换成了 `.njk` 模板，因此不同版本需要额外区分。

{% tabs New Sidebar Config %}
<!-- tab NexT 8.0.0 版本及以上-->
在博客根目录下的 `source/_data` 文件夹中新建 `sidebar.njk` 文件，并在 NexT 主题配置中启用文件。

``` diff _config.next.yml
 custom_file_path:
-  #sidebar: source/_data/sidebar.njk
+  sidebar: source/_data/sidebar.njk
```
<!-- endtab -->

<!-- tab NexT 8.0.0 版本以下-->
在博客根目录下的 `source/_data` 文件夹中新建 `sidebar.swig` 文件，并在 NexT 主题配置中启用文件。

``` diff _config.next.yml
 custom_file_path:
-  #sidebar: source/_data/sidebar.swig
+  sidebar: source/_data/sidebar.swig
```
<!-- endtab -->

{% endtabs %}

## 添加 Tag Cloud

首先安装 [`hexo-tag-cloud`](https://github.com/D0n9X1n/hexo-tag-cloud) 插件。

``` shell
npm install hexo-tag-cloud --save
```

{% tabs Sidebar tag cloud %}
<!-- tab NexT 8.0.0 版本及以上-->
添加相关代码写入 `sidebar.njk`。不喜欢标题的把 `<h3 class="widget-title">Tag Cloud</h3>` 去掉即可。

``` html source/_data/sidebar.njk
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
<!-- endtab -->

<!-- tab NexT 8.0.0 版本以下-->
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
<!-- endtab -->

{% endtabs %}

# 页面

在博客根目录下的 `source/_data` 文件夹中新建 `variables.styl` 文件。

## 页面圆角

在 `variables.styl` 文件里添加圆角半径值

``` stylus source/_data/variables.styl
// 圆角设置
$border-radius-inner     = 10px;
$border-radius           = 10px;
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

- 参考文档：[Hexo Next 博客自定义配置 -> 更改-sidebar-大小](https://yi-yun.github.io/Hexo-Next-Custom/#更改-sidebar-大小)

# 数学公式

## MathJax 渲染带编号公式导致行溢出的问题

在移植博客的时候发现，带编号的长公式（如 `equation` 和 `align` 环境）在窄屏幕上（如手机）显示时会出现行溢出的问题，通过 Google Chrome 检查了一番发现，`mjx-container` 在渲染后会多出 `min-width` 属性，且每个公式的 `min-width` 不同，导致 `overflow` 无法触发，因为 `min-width` 和公式宽度是相等的，不会存在 Overflow 的情况。

``` css
element.style {
    font-size: 121.3%;
    min-width: 35.348em;
    position: relative;
}
```

MathJax 的 [Github Issue](https://github.com/mathjax/MathJax/issues/2280) 中也出现了类似讨论，官方将这种行为解释为“为了避免公式和编号重叠”而做出的选择。然而实际操作中发现，在 Google Chrome 检查页面中，将 `min-width` 手动修改为 `0` 后，可以正常触发 Overflow，并生成水平方向的 Scroll Bar，且公式和编号没有重叠。

因此突发奇想，能不能指定 `!important` 属性，将生成的 `min-width` 强行覆盖掉呢？于是做了如下尝试：

``` stylus source/_data/styles.styl
mjx-container.MathJax.CtxtMenu_Attached_0 {
    min-width: auto !important;
}
```

然后带编号的公式就可以正常显示 Scroll Bar 了！

# 不蒜子

## 什么是不蒜子

[不蒜子](https://busuanzi.ibruce.info/)是 [Bruce](http://ibruce.info/) 开发的一款轻量级的网页计数器，可以将网站访问计数直接显示在网页上。

## 启用不蒜子

NexT 预置了不蒜子提供的网页计数功能。只需要在配置文件中启用即可。

``` yaml next/_config.yml
# Show Views / Visitors of the website / page with busuanzi.
# Get more information on http://ibruce.info/2015/04/04/busuanzi
busuanzi_count:
  enable: true                          # 启用不蒜子
  total_visitors: true                  # 网站总访客数
  total_visitors_icon: fa fa-user
  total_views: true                     # 网站总浏览数
  total_views_icon: fa fa-eye
  post_views: true                      # 文章浏览数
  post_views_icon: fa fa-eye
```

## 不蒜子和 Live2D 冲突的问题 （2020-11-27）

> Hexo 版本：5.2.0
>
> NexT 版本：8.0.2
>
> 不蒜子版本: 2.3

### 问题描述

添加 Live2D 前的不蒜子显示情况如下。

![添加 Live2D 前单篇博客的不蒜子显示情况](../images/post/2020-11-27-hexo-next-busuanzi-issues/2020-11-27-hexo-next-busuanzi-issues-010-Live2D-01.png?imageMogr2/thumbnail/!100p|watermark/2/text/QEpvc2ggR2Fv/font/YWhyb25iZC50dGY=/fontsize/14/dissolve/20/gravity/southeast/dx/5/dy/5)

![添加 Live2D 前网站的不蒜子显示情况](../images/post/2020-11-27-hexo-next-busuanzi-issues/2020-11-27-hexo-next-busuanzi-issues-020-Live2D-02.png?imageMogr2/thumbnail/!100p|watermark/2/text/QEpvc2ggR2Fv/font/YWhyb25iZC50dGY=/fontsize/14/dissolve/20/gravity/southeast/dx/5/dy/5)

添加 Live2D 后，不蒜子在页面中不显示。

![添加 Live2D 后单篇博客的不蒜子显示情况](../images/post/2020-11-27-hexo-next-busuanzi-issues/2020-11-27-hexo-next-busuanzi-issues-030-Live2D-03.png?imageMogr2/thumbnail/!100p|watermark/2/text/QEpvc2ggR2Fv/font/YWhyb25iZC50dGY=/fontsize/14/dissolve/20/gravity/southeast/dx/5/dy/5)

![添加 Live2D 后网站的不蒜子显示情况](../images/post/2020-11-27-hexo-next-busuanzi-issues/2020-11-27-hexo-next-busuanzi-issues-040-Live2D-04.png?imageMogr2/thumbnail/!100p|watermark/2/text/QEpvc2ggR2Fv/font/YWhyb25iZC50dGY=/fontsize/14/dissolve/20/gravity/southeast/dx/5/dy/5)

### 问题原因

经查，添加 Live2D 后网页的不蒜子部分源码的 `style` 从 `"display: inline;"` 替换为了 `"display: none;"`。

``` html 不蒜子不正常显示的网页源码
<span class="post-meta-item" title="阅读次数" id="busuanzi_container_page_pv" style="display: none;">
  <span class="post-meta-item-icon">
    <i class="fa fa-eye"></i>
  </span>
  <span class="post-meta-item-text"> 阅读次数：</span>
  <span id="busuanzi_value_page_pv">19</span>
</span>
```

``` html 不蒜子正常显示的网页源码
<span class="post-meta-item" title="阅读次数" id="busuanzi_container_page_pv" style="display: inline;">
  <span class="post-meta-item-icon">
    <i class="fa fa-eye"></i>
  </span>
  <span class="post-meta-item-text"> 阅读次数：</span>
  <span id="busuanzi_value_page_pv">18</span>
</span>
```

### 解决方法

在网页生成引擎中修改代码，去掉不蒜子部分的 `id` 和 `style` 属性，重新生成网页即可解决该问题。若将 `class` 属性一并删除，会导不蒜子部分和前面的其他显示部分缺少分隔符，如下图。

![ ](../images/post/2020-11-27-hexo-next-busuanzi-issues/2020-11-27-hexo-next-busuanzi-issues-050-Delimeter-01.png?imageMogr2/thumbnail/!100p|watermark/2/text/QEpvc2ggR2Fv/font/YWhyb25iZC50dGY=/fontsize/14/dissolve/20/gravity/southeast/dx/5/dy/5)

具体需要修改两个文件，`next/layout/_third-party/statistics/busuanzi-counter.swig` 中修改的是网页页脚部分的不蒜子显示，`next/layout/_macro/post.swig` 中修改的博客文章阅览数的不蒜子显示。

``` html next/layout/_third-party/statistics/busuanzi-counter.swig
{%- if theme.busuanzi_count.total_visitors %}
  <span class="post-meta-item" id="busuanzi_container_site_uv" style="display: inline;">
    <!-- ... -->
  </span>
{%- endif %}

<!-- ... -->

{%- if theme.busuanzi_count.total_views %}
  <span class="post-meta-item" id="busuanzi_container_site_pv" style="display: inline;">
    <!-- ... -->
  </span>
{%- endif %}
```

``` html next/layout/_macro/post.swig
{%- if not is_index and theme.busuanzi_count.enable and theme.busuanzi_count.post_views %}
  <span class="post-meta-item" title="{{ __('post.views') }}" id="busuanzi_container_page_pv" style="display: inline;">
    <!-- ... -->
  </span>
{%- endif %}
```

## 不蒜子和 Live2D 冲突的问题 （2023-04-08）

> Hexo 版本：6.3.0
>
> NexT 版本：8.15.1
>
> 不蒜子版本: 2.3

重新捣鼓博客的时候发现不蒜子又不显示了，依然已经不维护的 `hexo-helper-live2d` 插件导致的。2020 年的方法写的模棱两可，NexT 的一些网页架构也有了细微改变，因此将新的解决方法附于下方。很多方法是通过直接修改 NexT 主题下的源文件实现的，但是 NexT 并不推荐这样的写法，因为主题目录下的文件是会随着主题更新而被覆盖的，而且不便于使用 Git 管理。经过寻找，在 NexT 的 Github Issues 中找到了优雅的解决方法。

在 `source/_data/styles.styl` 中添加如下代码即可解决。

``` styl source/_data/styles.styl
#busuanzi_container_site_uv, #busuanzi_container_site_pv, #busuanzi_container_page_pv {
  display: inline !important;
}
```

> REF:
>
> 1. 上述解决方法来源：[【必读】更新说明及常见问题](https://github.com/next-theme/hexo-theme-next/issues/4#issuecomment-690864794)
>
> 2. 这篇文章分析了 `hexo-helper-live2d` 插件导致不蒜子不显示的根本原因：[busuanzi 访问量统计与 live2d 插件同时使用导致 busuanzi 不显示的根本原因以及解决方法](https://ouuan.moe/post/2022/08/busuanzi-and-live2d)

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
