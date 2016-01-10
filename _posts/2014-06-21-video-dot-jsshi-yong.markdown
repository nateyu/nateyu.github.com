---
layout: post
title: "Html5视频播放器Video.js使用"
date: 2014-06-21 23:13
comments: true
categories: html/css
---

[video.js][1]是一个HTML5媒体播放器， 在HTML5基本video功能上扩展了很多功能，例如， 快进，播放多少秒等等。这些都可以通过js来控制。

基本使用如下：

- 下载video.js, 下载地址：[http://www.videojs.com][1]；不过建议使用一些免费的cdn较好。
- 引入video.js。 video.js没有依赖其他的库可以直接引入， 如果你使用[Modernizr][2]调整兼容性的话， 在页面任何位置引入都行；如果没有使用[Modernizr][2]， 你只能在<head>里面引用。
- 使用video.js. 代码如下：

```
<video id="MY_VIDEO_1" class="video-js vjs-default-skin vjs-big-play-centered" controls
  preload="none" poster="http://www.videojs.com/img/poster.jpg"
  data-setup="{}">
   <source src="http://vjs.zencdn.net/v/oceans.mp4" type='video/mp4'>
   <source src="http://vjs.zencdn.net/v/oceans.webm" type='video/webm'>
   <p class="vjs-no-js">To view this video please enable JavaScript, and consider upgrading to a web browser that <a href="http://videojs.com/html5-video-support/" target="_blank">supports HTML5 video</a></p>
</video>
```
加入代码页面就可以使用了。

有几点需要说明一下:

####播放按钮居中
默认样式播放按钮在左上角， 可以加上 **vjs-big-play-centered** 类，就可以是播放按钮居中了。

####视频宽高自适应
有两种方式使视频自适应， 第一种是设置width:100%, height:100%

```
<video id="MY_VIDEO_1" class="video-js vjs-default-skin vjs-big-play-centered"
    width="100%"
    height="100%"
    style="object-fit:fill;" //视频缩放比例设置为填充，默认用的contain保证宽高比例
    controls
    preload="none" 
    poster="http://www.videojs.com/img/poster.jpg"
    data-setup="{}">

    ...
```

第二种，使用js在你resize窗口时设置video大小。

```
<script type="text/javascript">
    videojs("MY_VIDEO_1").ready(function(){
      var myPlayer = this;    
      var aspectRatio = 9/16; 

      function resizeVideoJS(){
        var width = document.getElementById(myPlayer.id()).parentElement.offsetWidth;
        myPlayer.width(width).height( width * aspectRatio );
      }

      resizeVideoJS(); 
      window.onresize = resizeVideoJS; 
    });
 </script>
```

####遇到的兼容问题

不知道是否是我使用不当， 在Window版Chrome上没有进度条显示， 在小米手机中不能最大化。


  [1]: http://www.videojs.com/
  [2]: http://modernizr.com/