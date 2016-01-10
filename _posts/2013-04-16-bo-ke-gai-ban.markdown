---
layout: post
title: "博客改版小结"
date: 2013-04-16 23:53
comments: true
categories: ruby
tags: ruby
---

  开始[Jekyll](http://jekyllrb.com/)的时候，朋友推荐用[Octopress](http://octopress.org/)的[fabric](https://github.com/panks/fabric)主题。用了一段时间之后发不是十分喜欢灰色基调。其次，Jekyll本身是个很简单的东西，经过Octopress封装之后反而感觉繁琐，想简化它。

于是，做了一下几点改动：

  1. 使用新的主题
  2. 去掉octopress换用原生态jekyll
  3. 不使用自定义扩展，将_plugins清空，因为github上使用了--safe模式，以免不兼容
  4. 避免之前octopress用户自定义的layout和include层层嵌套。

### 使用新主题
  在换掉[fabric](https://github.com/panks/fabric)之前，在[Jekyll Themes](http://jekyllthemes.org/)上浏览了一遍，主题虽然多也有不错的，但是总觉得不是自己喜欢的。直到在[Wix](http://www.wix.com/)上看到[当前主题](http://www.wix.com/website-template/view/html/750?originUrl=http%3A%2F%2Fwww.wix.com%2Fwebsite%2Ftemplates%2Fhtml%2Fbusiness-services%2Fadvertising-marketing%2F1&bookName=create-master-new&galleryDocIndex=1&category=business-services&viewMode=desktop)。喜欢的原因有两个，一个是是它使用的小方格背景我很喜欢，我平时用的笔记本专买这种小方格，能给人设计感；另一个就是他的设计上简洁但是不死板，我不大喜欢左右布局。

  Wix上面的模板是自动生成的，代码很糟糕。我要做的就是使用他模板的设计，然后从新写一遍html和css。html/css我比较熟悉，重写一遍不是难事。将原来的宽度改为900px方便阅读，然后加上天气预报和pm2.5指数。文章字体是仿照[简书](http://jianshu.io/)做的

### 去掉octopress换用原生态jekyll
  不想用octopress是因为他在jekyll基础之上进行大量的封装，安装了很多插件，这些东西不能直接放在github上面。你需要在本地生成html然后将html提交到master分支，源代码单独提交，有点麻烦。于是去掉octoress直接用jekyll，保留原来octopress的rakefile, 作为产生posts和提交[Github](https://github.com/nateyu/nateyu.github.com)用。

### 不使用插件或扩展
  Push到[Github](https://github.com/nateyu/nateyu.github.com)上之后，代码可以以两种方式运行，静态html件这个github page基本功能，还有就是直接上传jekyll目录但是是以safe模式运行，也就是不支持插件。所要想直接上传jekyll目录而不转换html，就不要使用插件。

### 避免之前octopress用户自定义的layout和include层层嵌套。
  [Octopress](http://octopress.org/)上的模板使用方便，但是封装较多，特别是_includes和_layouts。

### 可以进的地方

  1. 分页功能
  2. 搜索共能
  3. 响应式设计
  4. 多浏览器兼容性

### 探讨

  利用[Github API](https://developer.github.com/v3/repos/contents/)提交代码， 是否可以实现在线编辑blog

### 小结


  自己动手打造的自己喜欢的东西

  喜欢清爽亮丽的而又简单的风格

  尽量不用插件，轻便而又简单

