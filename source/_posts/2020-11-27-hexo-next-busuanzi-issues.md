---
title: Hexo NexT 主题不蒜子相关问题汇总
tags:
  - 博客
  - Hexo
  - Next
categories:
  - [Blog, Hexo]
abbrlink: e96c9b26
copyright: true
comments: true
date: 2020-11-27 11:15:21
---

# 什么是不蒜子

[不蒜子](https://busuanzi.ibruce.info/)是 [Bruce](http://ibruce.info/) 开发的一款轻量级的网页计数器，可以将网站访问计数直接显示在网页上。

# 启用不蒜子

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

# 不蒜子和 Live2D 冲突的问题 （2020-11-27）

<!-- more -->

> Hexo 版本：5.2.0
>
> NexT 版本：8.0.2
>
> 不蒜子版本: 2.3

## 问题描述

添加 Live2D 前的不蒜子显示情况如下。

<div align="center">
  <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-27-hexo-next-busuanzi-issues/2020-11-27-hexo-next-busuanzi-issues-010-Live2D-01.png?imageMogr2/thumbnail/!100p|watermark/2/text/QEpvc2ggR2Fv/fontsize/15/dissolve/60/gravity/southeast/dx/5/dy/5"/>
</div>

<div align="center">
  <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-27-hexo-next-busuanzi-issues/2020-11-27-hexo-next-busuanzi-issues-020-Live2D-02.png?imageMogr2/thumbnail/!100p|watermark/2/text/QEpvc2ggR2Fv/fontsize/15/dissolve/60/gravity/southeast/dx/5/dy/5"/>
</div>

添加 Live2D 后，不蒜子在页面中不显示。

<div align="center">
  <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-27-hexo-next-busuanzi-issues/2020-11-27-hexo-next-busuanzi-issues-030-Live2D-03.png?imageMogr2/thumbnail/!100p|watermark/2/text/QEpvc2ggR2Fv/fontsize/15/dissolve/60/gravity/southeast/dx/5/dy/5"/>
</div>

<div align="center">
  <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-27-hexo-next-busuanzi-issues/2020-11-27-hexo-next-busuanzi-issues-040-Live2D-04.png?imageMogr2/thumbnail/!100p|watermark/2/text/QEpvc2ggR2Fv/fontsize/15/dissolve/60/gravity/southeast/dx/5/dy/5"/>
</div>

## 问题原因

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

## 解决方法

在网页生成引擎中修改代码，去掉不蒜子部分的 `id` 和 `style` 属性，重新生成网页即可解决该问题。若将 `class` 属性一并删除，会导不蒜子部分和前面的其他显示部分缺少分隔符，如下图。

<div align="center">
  <img src="https://josh-blog-1257563604.cos.ap-beijing.myqcloud.com/img/2020-11-27-hexo-next-busuanzi-issues/2020-11-27-hexo-next-busuanzi-issues-050-Delimeter-01.png?imageMogr2/thumbnail/!100p|watermark/2/text/QEpvc2ggR2Fv/fontsize/15/dissolve/60/gravity/southeast/dx/5/dy/5"/>
</div>

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

# 不蒜子和 Live2D 冲突的问题 （2023-04-08）

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
