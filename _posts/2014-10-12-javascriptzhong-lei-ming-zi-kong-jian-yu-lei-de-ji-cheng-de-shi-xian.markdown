---
layout: post
title: "Javascript中类，名字空间和类的继承的实现"
date: 2014-10-12 17:54
comments: true
categories: javascript
---

最近在做一个微信项目，由于微信加载页面很慢，所以像jquery这些成熟的js库显得是个庞然大物。没办只能自己动手实现一些基础的东西，
首先，需要实现最基础javascript的类定义和类的继承，其次是名字空间。这几个是方便我们管理和组织代码最基础的，当然不是必须的，你也可以以最少代码方式将js写在你需要的地方；作为一个完整的项目我不建议这做。来看看我的具体实现吧。

###名字空间
Javascript中没有namespace概念，稍微面向对象一点，所有的对象跑在顶级作用域下是个很大的麻烦。所以首先要解决这个问题，实现的方式以一个空对象作为空间名字，如： BH.file.image.Upload等。我们在Upload空间下定义自己的类。

具体实现：

```
省略代码段...

namespace: function (nstr) {
      var fns = nstr.split('.'), pns = BH;
      if (fns[0] == 'BH') {
        fns.shift();
      }
      for (var i = 0; i <= fns.length - 1; i++) {
        pns[fns[i]] = pns[fns[i]] || {};
        pns = pns[fns[i]];
      }
      return pns;
    }

省略代码段...
```
方法很简单，一次遍历以'.'分割的空间名，然后设置为空对象即可，当然一定要讲起始空间保存这个是关键。最后返回最后一个空间作为当前对象。

使用方法：     
BH.namespace('BH.file.image.Upload');   
BH.file.image.Upload // 为空对象{}

###类定义
严格的讲javascript是没有类这个概念的，一切都是对象。我们为了面向对象方便，运用各种设计模式实现与其他语言中等同的类。这些实现方式不少于5种，比如：工厂模式，构造函数模式，原型模式等等，各有利弊。我构造函数和原型组合的方式。

实现如下：

```
  defines: function (nstr, prototype) {
      var nstr = nstr.split('.'),
          fn = nstr.pop(),
          ns = BH.namespace(nstr.join('.'));
      ns[fn] = prototype.constructor || function () {}; //设置构造函数为constructor，或者空函数
      ns[fn].prototype = prototype; //设置prototype
      prototype.constructor = ns[fn]; //设置prototype的constructor属性指向当前函数而不是Object
      return ns[fn];
  }
```

使用方法：

```
BH.defines('BH.file.image.Upload', {
  constructor: function() {}, //定义构造函数
  A: function() {},
  attrB: 'attr'
});
```


###类继承
和类定义一样，javascript中也是用设计模式实现等同其他语言的继承方式。实现方式很多，我才用目前认为是最好的一种方式--寄生组合式继承。这个也是YUI中实现继承的方式。

实现如下：

```
extend: function (sub, parent, options) {
  var emptyClass = function () {};
  emptyClass.prototype = parent.prototype;
  sub.prototype = new emptyClass();
  sub.prototype.constructor = sub;
  sub.superclass = superc.prototype;

  if (options) {
    for (var i in options) {
      subc.prototype[i]=options[i];
    }
  }

  return sub;
}
```

采用一个空函数做为构造函数来置空父类的构造函数，同时继承父类的prototype，这样做的目的为了避免引用数据类共享带来的问题。然后用options覆盖父类属性和方法，如果需要的话。

使用方法：  
BH.defines('BH.a.B');   
BH.extend(BH.a.B, BH.a.C { name: 'B'});


