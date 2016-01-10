---
layout: post
title: "Disqus评论参数说明"
date: 2014-06-21 12:14
comments: true
categories: Javascript
---

在博客中引入Disqus评论功能时， 默认只需要添加一个 **disqus_shortname** 参数就可以， 这个参数是你在申请comments组是的标识。代码如下：

```javascript
<script type="text/javascript">
  var disqus_shortname = 'test'; //这个就是你唯一的标识
   
  (function () {
    var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
    dsq.src = 'http://' + disqus_shortname + '.disqus.com/embed.js';
    (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
  }());
</script>
```
  
上面代码添加到页面就可使用评论功能了。

###通过设置disqus_url参数标识当前页面

但是Disqus是按照页面url来生成评论的， 什么意思呢？就是在提交评论时默认时按照你当前评论的文章的url作为key存储评论， 也就是通过javascript去window.location.href作为值。一般情况不同文章的访问地址肯定是不一样的， 使用也不会出现问题。但是我们经常在博客首页放最近发表的文章， 也就是相同的博客地址对应不同文章， Disqus默认按照url存储和读取评论就会导致针对不同文章的评论混淆在一起。解决这个问题只需要在设置一个 **disqus_url** 就可以了。看如下配置：

```javascript
<script type="text/javascript">
  var disqus_shortname = 'test'; //这个就是你唯一的标识
  var disqus_url = window.location.origin + 'post.url'; //域名加上文章路径，也可以是文章标题， 总之能区分不同文章就行
  (function () {
    var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
    dsq.src = 'http://' + disqus_shortname + '.disqus.com/embed.js';
    (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
  }());
</script>
```

加上disqus_url参数后Disqus就不在取浏览器url作为评论标识了， 而是使用你设置url。注意， 这里的url要用完整http开头， 不然不能识别。

###其他的参数
 
 - **disqus_shortname**: 上面说到过你申请评论的唯一标识， 必填项。
 - **disqus_identifier**: 当前页面的标识， 没设置就使用url。但道理是用这个来标识不同页面， 但是我没有设置成功。
 - **disqus_title**: 评论标题， 这个是Disqus后台用的， 如果没有就去html页面的title.
 - **disqus_url**: 当前页面的url, 没有设置就用window.location.href。上面例子就是用这个参数。
 - **disqus_category_id**: 后台设置的category, 在settings/advanced/里面设置。不知道用途是什么。
 - **disqus_disable_mobile**: 是否开启移动设备支持。