---
layout: post
title: "rails的中的MIME类型"
date: 2014-09-08 21:40
comments: true
categories: ruby
---

Rails开发中经常使用不同的请求格式来处理不同的响应，最常见的是同一个action对html/text和json格式的响应不同的数据。 那么, Rails中有哪些响应格式已经是怎么处理这些请求格式呢？本文详细讲讲rails中的处理方式。

###什么是MIME?
先看看MIME的标准定义。MIME(Multipurpose Internet Mail Extensions)多用途互联网邮件扩展类型，是来设定某种扩展名的文件用一种应用程序来打开的方式类型，当该扩展名文件被访问的时候，浏览器会自动使用指定应用程序来打开。   
MIME早期是应用于电子邮件系统，后来也应用到了浏览器中。浏览器会根据MIME类型来打开不同的文件，如mp3文件。早期的HTTP协议中是没有附加数据类型信息，所有传输的数据都被解释为超文本标记语言（HTML文档），当MIME被支持以后HTTP传输的不仅是普通的文本，而是我们现在看到的各种个样的数据形式了。

###MIME的定义和组成
MINE类型是由两部分组成，前面是数据的大类别，文本text、图象image等，后面定义具体的种类。例如：

```
//大类别为text
 html文本   .html text/html
 xml文档    .xml  text/xml
 普通文本    .txt  text/plain

//大类别为application
 XHTML文档  .xhtml application/xhtml+xml
 pdf文档    .pdf application/pdf

//大类别为image
 png图像    .png image/png
 git图形    .gif image/gif
```

MIME实际上是由一个专门的组织IANA来确认标准的MIME类型，但是由于互联网发展速度太快，由IANA来确定标准远远跟不上应用程序的发展速度。因此，现在多数采用事实标准，也就是由服务器和浏览器共同承认的MIME类型即可；通常web服务器(下面要介绍rails中的mime类型)和浏览器都已默认设置了常见的mime类型，当确实需要使用一些不常见的mime类型时，可以在服务器和浏览器同时设置，以好让浏览器解析。   
在服务器端，是通过请求的后缀名来识别不同的mime类型的，因此服务器中必须定义有请求后缀与mime类型的对应关系（rails中是在action_dispatch/http/mime_types.rb中定义）。
浏览器在接受响应数据时，接受的服务器的数据流，也就是说不会解析文件的名字，要让浏览器识别不同的类型的文件数据就需要在响应数据中设置mime类型信息。服务器在响应数据前，首先是要设置数据的MIME类型信息，这个是通过http信息头部的Content-type关键字进行设定的。

```
//冒号之后必须一个空格
Content-type: text/html
```

###Rails中的MIME类型
接下来，我们看看Rails中是如何处理mime类型的。我们上面讲过，服务端是通过请求后缀来识别mime类型的。Rails的请求后缀可以通过request.formart得到，具体看看format的处理细节：

```
def formats
  @env["action_dispatch.request.formats"] ||=
    if parameters[:format]
      Array(Mime[parameters[:format]])
    elsif use_accept_header && valid_accept_header
      accepts
    elsif xhr?
      [Mime::JS]
    else
      [Mime::HTML]
    end
end
```

从上面代码可以看到，Rails首先会接受我们自己设定的format参数，这个参数可以通过3中方式设置：   
1. url后缀，如：users.json, users/1.json   
2. 通过foramt参数传递，如：{id: 1, format: 'json' }   
3. 路由中设置默认formart, 如：defaults: { format: :json }    

如果没有指定format参数rails会检查你请求头部，也就是accepts方法做的事情，其实也就是查看Content-type。   
上面都没有找到mime类型并且为异步请求，就直接设置format为'js'类型(注意，不是json)。    
最后都没有找到，就按普通html处理，也就是'text/html'。    

前面已经提到过，服务器会维护一个format和mine类型的对应关系，rails中是由Mime::Type这里处理，调用Mime::Type.register来注册新的mime类型，具体见action_dispatch/http/mime_types.rb文件。

###不要用json类型来判断是否为异步请求
刚开始学javascript时候，很容易犯的一个错误就是使用json请求后缀来判断是否是ajax请求，这个绝大多数是没有问题的，因为将ajax请求设置为json后缀几乎是约定俗成了。实际上这样做的不靠谱的，上面的代码可以看出判断异步请求rails是用xhr?方法，这个方法是这样做的:

```
def xml_http_request?
  @env['HTTP_X_REQUESTED_WITH'] =~ /XMLHttpRequest/i
end
alias :xhr? :xml_http_request?
```

很明显，用的http请求的'XMLHttpRequest'关键字来判断是否是异步，而非请求头部的Content-type。

总结，本文讲述了MIME类型的定义，组成部分和作用，以及Rails中对MIME类的处理，希望大家有所了解。
