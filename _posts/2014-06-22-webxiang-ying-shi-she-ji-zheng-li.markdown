---
layout: post
title: "Web响应式设计笔记整理"
date: 2014-06-22 23:32
comments: true
categories: html/css
---

最近在开发web相关功能， 整理下相关知识点。主要内容来自《响应式Web设计HTML5和CSS3实战》笔记整理。

###响应式设计栅格系统

 1. Semantic (http://semantic.gs);
 2. Skeleton (http://getskeleton.com);
 3. Less Framework (http://lessframework.com);
 4. 1140 CSS Grid (http://cssgrid.net);
 5. Columnal (http://www.columnal.com)。

###在线实例
- 响应式设计： http://mediaqueri.es
- 3D动画：http://demo.marcofolio.net/3d_animation_css3

###窗口大小调试工具
- IE: Microsoft Internet Explorer Developer Toolbar(http://www.microsoft.com/download/en/details.aspx?id=18359)
- Safari: ResizeMe(http://web.me.com/aaronholla/Safari_Extensions/ResizeMe.html) , Resize(http://resizeSafari.com)
- Firefox: Firesizer(https://addons.mozilla.org/en-US/firefox/addon/firesizer/)
- Chrome:  Windows Resizer(https://chrome.google.com/webstore/detail/kkelicaakdan- hinjdeammmilcgefonfh)

###媒体查询：
媒体查询支持的浏览器：Firefox 3.6+、Safari 4+、Chrome 4+、 Opera 9.5+、iOS Safari 3.2+、Opera Mobile 10+、Android 2.1+和 Internet Explorer 9+

css2 link标签媒体查询：

```
<link rel="stylesheet" type="text/css" media="screen" href="screen-styles.css”>
<link rel="stylesheet" media="screen and (orientation: portrait)" href="portrait- screen.css" />
<link rel="stylesheet" media="not screen and (orientation: portrait)" href="portrait- screen.css" />
<link rel="stylesheet" media="screen and (orientation: portrait) and (min-width: 800px)" href="800wide-portrait-screen.css" />
<link rel="stylesheet" media="screen and (orientation: portrait) and (min-width: 800px), projection" href="800wide-portrait-screen.css" />
```

css3样式表媒体查询：

```
@media screen and (max-device-width: 400px) { h1 { color: green } }
@import url("phone.css") screen and (max-width:360px); 
```

**媒体查询内容**  
width: 窗口宽度。  
height: 窗口高度。  
device-width: 设备屏幕的宽度  
device-height: 设备屏幕的高度  
orientation: 检查设备处于横向还是纵向。   
aspect-ratio:基于窗口宽度和高度的宽高比。 例如：aspect-ratio: 16/9。  
device-aspect-ratio: 设备渲宽度和高度的宽高比。   
color: 每种颜色的位数。例如 min-color: 16 会检测设备是否拥有 16 位颜色。  
color-index:设备的颜色索引表中的颜色数。值必须是非负整数。  
monochrome:检测单色帧缓冲区中每像素所使用的位数。值必须是非负整数,如：monochrome: 2。 
resolution:用来检测屏幕或打印机的分辨率,如 min-resolution: 300dpi。还可以接受每厘米像素点数的度量值,- 如 min-resolution: 118dpcm。  
scan:电视机的扫描方式,值可设为 progressive(逐行扫描)或 interlace(隔行扫描)。如 720p HD 电视(720p 的 p 即表明是逐行扫描)匹配 scan:progressive，而 1080i HD 电视(1080i 中的 i 表明是隔行扫描)匹配 - scan: interlace。   
grid:用来检测输出设备是网格设备还是位图设备。   

IE8以下媒体查询：Respond.js(https://github.com/scottjehl/Respond)是为 Internet Explorer 8及更低版本增加 媒体查询支持的最快的 JavaScript 工具,但它目前无法解析 CSS 的@import 命令。


###Reset CSS（http://www.cssreset.com/）
HTML4  
1. http://meyerweb.com/eric/tools/css/reset/  
2. http://simplebits.com  
3. http://yui.yahooapis.com/3.17.2/build/cssreset/cssreset-min.css

HTML5  
normalize.css(http://necolas.github.com/normalize.css/)


阻止移动浏览器自动调整页面大小:

```
<meta name="viewport" content="initial-scale=1.0, user-scalable=no" /> user-scalable=no 即是禁止缩放。
<meta name="viewport" content="width=device-width,initial-scale=1.0" /> 浏览器将按照其窗口的实际大小来渲染页面, 将宽度设置为设备宽度。
```

###流体布局

使用百分比布局创建流动的弹性界面,同时 使用媒体查询来限制元素的变动范围。将这两者组合到一起构成了响应 式设计的核心,基于此可以创造出真正完美的设计。 

固定宽度改为百分比：

     目标元素宽度÷上下文元素宽度=百分比宽度

用em替换px:  
1.是那 些使用 Internet Explorer 6 的用户也将能够缩放文字  
2.如果我们给 <body>标签设置文字大小为 100%,给其他文字都使用相对单位 em,那这些文字都会受 body 上的初始声明的影响。这样做的好处就是,如果在完成了所有文字排版后,客户又 提出将页面文字统一放大一点,我们就可以只修改 body 的文字大小,其他所有文字也会 相应变大

>em 究竟是什么? em 是书面形式的大写字母“M”的简称,发音和 M 相同。以前,“M” 常被用来测定某种字体的大小,因为它是英文字母中最大(最宽)的字 母。如今,em 作为一个测量单位,指的是特定字母的宽度和高度相对 于特定字体磅值的比例。 

弹性图片:

```
img,object,video,embed { max-width: 100%; } 
```
网页语言列表：
http://www.iana.org/ assignments/language-subtag-registry

###解决跨浏览器问题 
HTML5/CSS3修正：Modernizr(http://www.modernizr.com)。  
IE6、7、8 追加min/max媒体查询功能:  Respond.js(https://github.com/scottjehl/Respond)   
将导航链接转换为下拉菜单：  
1. http://webdesignerwall.com/tutorials/css-responsive-navigation-menu  
2. http://webdesignerwall.com/demo/responsive-menu/

