---
layout: post
title: "Size方法的记录"
date: 2014-07-13 23:30
comments: true
categories: Ruby
---
Ruby数组的size方法本身没什么好说的，写这篇博客起源于一次code review, 在has_many关系中使用size方法。

###has_many关系

当我们使用has_many宏定义时， 实际上是建立一个model到数据表的映射机制(或者反射更恰当)，它会将你一些操作转化为相应的数据库queries，
具体定义于ActiveRecord::Relation类。这些映射机制最大的特点是惰性加载， 也就是只有当你调用方法时数据才加载。

```ruby
  class Order < ActiveRecord::Base
     has_many: :items
  end

  order = Order.where(id: 1)
  order.items.count
  order.items.loaded? # => false
  order.items.length  # loaded? => true
  order.items.size
```
上面代码可以看到order.items.count， order.items并没加载数据， 但是order.items.length之后数据是加载的。原因在于count是Rection中的方法， 直接转化为数据的queries。如下：

```sql
  ELECT COUNT(*) FROM `items` WHERE `items`.`order_id` = 1
```

而length不是Rection的方法， 事情被分成两步，先执行items加载数据然，后在返回的数组上执行length方法。改过程也需要查询数据库，不过
是不是count而是：

```sql
  SELECT `items`.* FROM `items` WHERE `items`.`order_id` = 1
```

那么， size有事怎么回事呢？在Ruby中本身size是length的别名， 但是Rails中不是单纯别名了， 虽然多数时候表现得就是别名。我们来看看
size在Relction中的具体定义：

```ruby
  # Returns size of the records.
  def size
    loaded? ? @records.length : count
  end
```

一目了然， 通判断数据是否加载决定使用count还length。因此这里我总结一下size方法调用步骤：

1.通过loaded?判断关联数据时候已经加载  
2.如果关联数据已经被加载， 那么就在数组上使用length方法得到数量  
3.如果关联数据没有被加载， 直接在数据库上使用count统计出数量  

因此， size在关联数据没有加载的时候和count是等同的；在关联数据已经加载时， size直接计算数组元素大小不查询数据库， count则每次都要查询数据库。

是不是这样size的效率一定就优于count呢？这个完全取决于ruby对数组元素的统计和mysql的count哪个更快了。还有待进一步实验证明。

###will_paginate的size
order.items我们通常加上分页， 如何计算分页总数， 有人也是在分页结果后面加上size:

```ruby
  order.items.paginate(page: 1).size
  或者
  order.items.size
```

上面方法调用没有错， paginate返回的结果时Array的子类， 可以直接调用size方法。然而更有效率的做法是：

```ruby
  order.items.paginate(page: 1).total_entries
```
paginate在实例化的时候会对本身调用size方法， 然后记录在@total_entries变量里面， 之后每次使用就不用重复调用size方法了，节省大量开销。

