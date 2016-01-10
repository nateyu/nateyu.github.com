---
layout: post
title: "Javascript新接口"
date: 2014-10-26 23:15
comments: true
categories: javascript
---

随着Html5的到来，javascript也得到极大的发展，产生了很多新API，给web应用带来很大的便利。

###requestAnimationFrame()
这个API是给我们来创建动画用，在html5之前我们创建动画基本是用setInterval()来循环执行。

```
(function(){
  function updateAnimations(){
  doAnimation1();
  doAnimation2(); //其他动画
}
  setInterval(updateAnimations, 100);
})();
```

这个设置动画最主要的问题是，显示器本身也需要刷新，设置时间间隔导致动画不平滑。

requestAnimationFrame()只是告诉浏览器需要执行动画，至于多少时间执行由浏览器自己决定和优化。

```
function updateProgress(){
        var div = document.getElementById("status");
        div.style.width = (parseInt(div.style.width, 10) + 5) + "%";
        if (div.style.left != "100%"){
            mozRequestAnimationFrame(updateProgress);
} }
mozRequestAnimationFrame(updateProgress);
```

moz前缀大家应该明白的吧，还有webkitRequestAnimationFrame与msRequestAnimationFrame。

###Page Visibility API
页面可见性api, 能够好的帮助我们了解用户是与当前页面互交。也就是说，如果当前页面别最小化，或者被其他程序遮挡住，那么我们可以将该页面的一些动画效果后后台服务可以暂停。这样大大节省开销。

Page Visibility API由3个部分组成：   
1.document.hidden:表示页面是否隐藏的布尔值。页面隐藏包括页面在后台标签页中或者浏览
器最小化。   
2.document.visibilityState, 表示下列 4 个可能状态的值:(1)页面在后台标签页中或浏览器最小化;(2)页面在前台标签页中;(3)实际的页面已经隐藏,但用户可以看到页面的预览(就像在 Windows 7 中,用户把鼠标移动到
任务栏的图标上,就可以显示浏览器中当前页面的预览);(4)页面在屏幕外执行预渲染处理     
3.visibilitychange 事件

```
if (document.hidden || document.msHidden || document.webKitHidden){ //页面隐藏了
} else { //页面未隐藏
}
```

###Geolocation API
地理位置的api, 也就是浏览器能获得用户的地理位置经纬度。这个在很多见的到。是由navigator.geolocation对象来实现。

###File API
File API让web中也能访问计算机中的文件成为可能而不是简单使用\<input type="file"\>字段。

每个 File 对象都有下列只读属性。  
1. name:本地文件系统中的文件名。   
2. size:文件的字节大小。   
3. type:字符串,文件的 MIME 类型。   
4. lastModifiedDate:字符串,文件上一次被修改的时间   

```
var filesList = document.getElementById("files-list");
EventUtil.addHandler(filesList, "change", function(event){ 
var files = EventUtil.getTarget(event).files,
  i = 0,
  len = files.length;
while (i < len){
  console.log(files[i].name + " (" + files[i].type + ", " + files[i].size +" bytes) ");
  i++; }
});
```

###Web 计时
主要是用来检查页面的性能，Web 计时机制的核心是 window.performance 对象。方法众多， 如：   
1. redirectCount:页面加载前的重定向次数。   
2. navigationStart:开始导航到当前页面的时间。   

###Web Workers
这个绝对是神器之一，js可以像其他语言一样，讲一些耗时间的操作使用线程、后台进程等放在后台进行。