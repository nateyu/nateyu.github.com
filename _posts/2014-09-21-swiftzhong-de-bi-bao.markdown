---
layout: post
title: "Swift中的闭包"
date: 2014-09-21 21:39
comments: true
categories: ios
---

闭包的概念来自函数编程，很多语言都支持。Swift对闭包的定义可能是最明确易懂的(与js和ruby比较，哈哈)，来看看官方定义。

>
闭包是自包含的函数代码块，可以在代码中被传递和使用。
闭包可以捕获和存储其所在上下文中任意常量和变量的引用。 这就是所谓的闭合并包裹着这些常量和变量，俗称闭包。

非常明确的指出闭包的实质，一、闭包是可以被传递的代码块，二、闭包可以访问所处上下文(context)中的变量和常量。这里请记住一个词上下文(context), 对于闭包理解十分重要。

###简述一下什么是下上下文环境   
context这个词在编程语言中是一个比较标准的专业术语，每种语言的文法必须要明确定义的。简单说，当我们函数调用时都会有个执行环境，这个执行环境主要是绑定相关的变量，对象等等。换句话说执行环境是伴随这当前的函数生命周期的一些对象和变量，当函数生命周期结束部分对象和变量随之消失。而闭包则可以在函数生命周期以外访问这些资源。

###Swift中闭包形式    
1 全局函数是一个有名字但不会捕获任何值的闭包   
2 嵌套函数是一个有名字并可以捕获其封闭函数域内值的闭包   
3 闭包表达式是一个利用轻量级语法所写的可以捕获其上下文中变量或常量值的匿名闭包   

*闭包表达式语法*   

```
{ (parameters) -> returnType in
    statements
}
```

用一对大括({})号包含，指定参数和返回值。


*来自官的举例*   

对数组排序   
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]   

1、标准的使用，闭包函数

```
func backwards(s1: String, s2: String) -> Bool {
    return s1 > s2
}
var reversed = sort(names, backwards)
```

2、使用闭包表达式

```
reversed = sort(names, { (s1: String, s2: String) -> Bool in
    return s1 > s2
    })

```

3、自动推断类型参数类型和返回值

```
reversed = sort(names, { s1, s2 in return s1 > s2 } ) //参数类型和返回类型省略
```

4、单表达式闭包隐式返回

```
reversed = sort(names, { s1, s2 in s1 > s2 } ) //省略return
```

5、参数名称缩写

```
reversed = sort(names, { $0 > $1 } ) //通过$0,$1,$2来顺序调用闭包的参数
```

6、运算符函数
Swift 的String类型定义了关于大于号 (>) 的字符串实现，其作为一个函数接受两个String类型的参数并返回Bool类型的值。因此可以更简洁的写为

```
reversed = sort(names, >)
```

这个有点复杂了，需要进一步了解运算符函数相关内容了。


*尾随闭包*   
尾随闭包是另外一种闭包的调用方式，当你将闭包作为最后一个函数传递时，可以将其放在函数参数列表以外。还是以sort为例：

正常使用

```
var reversed = sort(names, backwards)
reversed = sort(names, { $0 > $1 } )
```

尾随闭包，闭包体位于函数调用后面

```
reversed = sort(names) { $0 > $1 }
```

###比较swift, ruby和javascript中的闭包
我们看几个不同语言中的例子来看看什么是闭包。

Javascript中闭包主要是通过函数传递或者嵌套定义来实现

```
function a() {
  var counter = 1;
  renturn function b() {
    return counter ++;
  }
}

c = a();
c() // counter为2, 一般情况执行完a()之后，counter就会被回收，但是这里因为闭包b将它带出了作用域
```

Ruby不支持嵌套定义函数，闭包是通过Proc来实现

```
def a
  counter = 1
  return Proc.new { counter += 1 }
end

c = a
c.call  # counter为2
```

ios中闭包

```

func a() -> () -> Int {
    var counter = 1;
    func b() -> Int {
      return ++counter
    }
    return b
}

var c = a()
c() //counter为2
```

通过从上面例子我们可以看出，1. 要形成闭包首先得支持代码块传递，2. 闭包的行为是能够将变量带出在当前执行上下文环境以外，也就是本身作用域之外还能继续访问。

