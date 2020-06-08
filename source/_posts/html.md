---
title: html
date: 2018-12-15 12:03:45
categories:
- html
tags:
- html
---

## DOCTYPE

我们在编写html的时候往往第一行就是DOCTYPE的声明，那么这个DOCTYPE是什么呢？DOCTYPE=Document Type。也就是文档类型，众所周知，html作为一种在web时代使用的超文本标记语言，最基本的功能就是作为文档传达信息。

而html（1993）经过20几年的发展，其中发展出了众多标准，DOCTYPE的作用之一就是指明文档类型，如html的最新版本html5的DOCTYPE就是`!DOCTYPE html`。此外，在浏览器端，DOCTYPE还指明了其渲染文档的模式，明确说来就是是否采用怪异模式(quirks mode)渲染，与之对应的是标准模式(standard mode)，声明一个合法的DOCTYPE可以使得浏览器以标准模式渲染，至于渲染模式存在的原因还要归结于历史因素，简言之就是为了解决兼容性的问题。

DOCTYPE起初的目的是用作文档验证，我们在声明中指定一个DTD(Document Type Defination)的链接，该DTD用于解析和验证文档的合法性，而html5没有指定DTD，所以html5的DOCTYPE可以如此简洁。

附HTML4的DOCTYPE，`<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "https://www.w3.org/TR/html4/strict.dtd">`
<!-- more -->

## 语义化(Semantic HTML)

### 语义化的概念
谈及HTML，总免不了要提及语义化的概念。作为标记语言的HTML的基本元素就是element，这些element都有自己特定的含义，开发者应遵循标签本身的固有语义来编写HTML。还是举个例子，比如我们看到nav标签就可以知道这个是一个导航元素。

### 语义化的意义

我们学习一个新概念的时候，首先要从感性的角度来学习这个概念，更重要的是，我们要知道为什么会产生这个概念，存在必有其合理性，也就是这个概念存在的意义到底是什么呢？大体上，语义化有以下三个意义：
- 提升可读性与可维护性
- 搜索引擎优化(SEO)
- 提升无障碍性(Accessibility)

## 扩展性

我们不仅可以通过html标准去编写html，也可以自定义化我们自己的html文档，这就是html的可扩展性，具体体现在以下方面：
- meta
- data-*
- link
- JSON-LD   

### meta扩展

```html
<!-- 编码 -->
<meta charset="UTF-8">

<!-- 指定HTTP Header -->
<meta http-equiv="Content-Security-Policy"
  content="script-src 'self'">

<!-- SEO 搜索引擎优化 -->
<meta name="keywords" content="关键词">
<meta name="description" content="页面介绍">

<!-- 移动设备Viewport -->
<meta name="viewport" content="initial-scale=1">

<!-- 关闭iOS电话号码自动识别 -->
<meta name="format-detection" content="telphone=no">

<!-- 360浏览器指定内核 -->
<meta name="renderer" content="webkit">

<!-- 指定IE渲染模式 -->
<meta http-equiv="X-UA-Compatible" content="IE=edge">
```
此外，MDN也提及关于一些站点在社交媒体的分享的展示优化，比如facebook的og(open graph data)
```html
<meta property="og:image" content="https://developer.cdn.mozilla.net/static/img/opengraph-logo.dc4e08e2f6af.png">
<meta property="og:description" content="The Mozilla Developer Network (MDN) provides
information about Open Web technologies including HTML, CSS, and APIs for both Web sites
and HTML5 Apps. It also documents Mozilla products, like Firefox OS.">
<meta property="og:title" content="Mozilla Developer Network">
```
在CNN这样的新闻站点的meta标签中也有体现，因为网站的流量可能不仅来自于搜索引擎，还有很大一部分来自于类似facebook或者微博这样的社交媒体分享，而我们在meta标签中应用各个媒体的规则，就可以在这样的社交媒体分享中展示更定制化的信息预览。

### data-*

可以通过data前缀标签自定义属性，并且可以通过dataset api获取html中这些自定义的属性，格式为元素.dataset.*。dataset本质就是一个map的数据结构。

### link
```html
<!-- 引入 CSS -->
<link rel="stylesheet" href="style.css">

<!-- 浏览器预加载 -->
<link rel="dns-prefetch" href="//example.com">
<link rel="prefetch" href="image.png">
<link rel="prerender" href="http://example.com">

<!-- favicon -->
<link rel="icon" type="image/png" href="myicon.png">

<!-- RSS -->
<!-- 供阅读器订阅RSS -->
<link rel="alternate" type="application/rss+xml" href="/feed">
```

rel意指relationship，表明外链文档和当前文档的关系(relationship)。浏览器预加载的意义是增强站点的性能，通过预先加载某些资源，提前下载的方式使得后续对这些资源的访问更快。这有一篇讲述prefetch等的文章[prefetch](https://css-tricks.com/prefetching-preloading-prebrowsing/)

### JSON-LD

JSON-LD意思就是JavaScript对象标记-链接数据(Javascript object notation-link data)，是为了向搜索引擎提供更好的页面爬取和展示。关于JSON-LD，这有一篇很好的文章[JSON-LD](https://moz.com/blog/json-ld-for-beginners)

## Web无障碍(Accessibility)

让任何人不因为身体心理，技术缺陷，都能够平等的获取网页上的媒体内容

- WCAG(Web Content Accessibility Guideline)
- ARIA(Accessible Rich Internet Application)

### 提升无障碍性

- 为`img`提供`alt`属性：`img`无法正常显示的替代性文本
- `noscript`:提供一种禁用JS之后的替代方案
- `input`和`label`对应：扩大表单的点击范围
- 图形验证码与语音验证码：对视力障碍或者听力障碍者友好
- 文字和背景有足够对比度
- 键盘可操作：对用鼠标不方便的用户友好，不许在弹出层的下面一层focus