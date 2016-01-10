---
layout: post
title: "网页字体"
date: 2014-07-19 23:41
comments: true
categories: html/css
---

> 一直对网页字体不敏感，今天重要看到一篇不错的[文章][1]，做个摘要。

###操作系统预装的字体

**Windows操作系统：**

* 黑体：SimHei
* 宋体：SimSun
* 新宋体：NSimSun
* 仿宋：FangSong
* 楷体：KaiTi
* 仿宋GB2312：FangSongGB2312
* 楷体GB2312：KaiTiGB2312
* 微软雅黑：Microsoft YaHei （Windows 7开始提供）

**OS X操作系统：**

* 冬青黑体: Hiragino Sans GB （SNOW LEOPARD开始提供）
* 华文细黑：STHeiti Light （又名STXihei）
* 华文黑体：STHeiti
* 华文楷体：STKaiti
* 华文宋体：STSong
* 华文仿宋：STFangsong

如果用户装了MicroSoft Office，还会多出一些字体。

* 隶书：LiSu
* 幼圆：YouYuan
* 华文细黑：STXihei
* 华文楷体：STKaiti
* 华文宋体：STSong
* 华文中宋：STZhongsong
* 华文仿宋：STFangsong
* 方正舒体：FZShuTi
* 方正姚体：FZYaoti
* 华文彩云：STCaiyun
* 华文琥珀：STHupo
* 华文隶书：STLiti
* 华文行楷：STXingkai
* 华文新魏：STXinwei

###英文字体优先于中文字体
font-family应该优先指定英文字体，然后再指定中文字体。否则，中文字体所包含的英文字母，会取代英文字体, 很丑陋。  
为了保证兼容性，中文字体的中文名称和英文名称，应该都写入font-family。比如，”微软雅黑”的英文名称是Microsoft YaHei。  
此外，中文字体的中文名称，以及由多个单词组成的英文名称，应该放在双引号内。

###Windows平台和Mac平台
由于Windows和Mac的中文字体没有交叉，所以应该同时为两个平台指定字体。  
常见的做法是，Windows平台指定”微软雅黑”（Microsoft YaHei），Mac平台指定”华文细黑”（STXihei）。

###衬线体和无衬线体
> 所谓”衬线体”（Serif），指的是笔画的末端带有衬线的字体。

衬线体装饰性强，往往用于标题；无衬线体清晰度好，往往用于正文。


  [1]: http://blog.jobbole.com/73722/