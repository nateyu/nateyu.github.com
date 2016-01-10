---
layout: post
title: "Rails的accepts_nested_attributes_for使用和原理"
date: 2014-06-15 22:33
comments: true
categories: ruby
---

当有两个存在one_to_one或者one_to_many的时候，我们可以用accepts_nested_attributes_for来更新关联对象。例如：
有Article对象模型， 存在一个author关联和多个comments关联

```ruby
  class Article < ActiveRecord::Base
    has_one :author
    has_many :comments
    ...
  end
```

在没有accepts_nested_attributes_for的时候， 要更新author和comments需要两步， 先创建子对象然后添加到当前对象，最麻烦的不是这个而是form表单，
你需要手动为关联对象设置名字, 代码如下：

```ruby
  <%= form_for @article do |f|
    author: <%= f.text_field 'article[:author][:name]' %>
    comments: <%= f.text_field 'article[:comments][][:content]' %>
  <% end %>
```

使用accepts_nested_attributes_for之后， 事情将被变得简单很多：

```ruby
  class Article < ActiveRecord::Base
    has_one :author
    has_many :comments
    
    accepts_nested_attributes_for :author, :comments #设置nested属性
  end

  class ArticlesController < ApplicationController
    def create
      @article = Article.new params[:article]
      @article.save
    end
  end

  <%= form_for @article do |f|
    <%= f.fields_for :author do |builder|%> #fields_for来设置nested关联
      author: <%= builder.text_field :name %>
    <% end %>

    <%= f.fields_for :comments do |builder|%>
      comments: <%= builder.text_field :content %>
    <% end %>
  end %>
```

可以看到整个事情变得简单不少了。

**那么， 使用accepts_nested_attributes_for之后发生什么了呢？**

Rails3之后的新特性accepts_nested_attributes_for属于ActiveRecord::NestedAttributes module, 调用该方法之后在Article上做以下几件事情：

```
1 检查默认参数:allow_destroy, :reject_if, :limit, :update_only，同时将默认参数分
  别与author，comments绑定
2 将author和comments的autosave选项设置为true
3 设置autosave相关的callback
4 在Article中定义author_attributes=和comments_attributes=实例方法。
  当然之前先要检查xxx_attributes=方法是否存在， 如果存在删除重新定义
```

### accepts_nested_attributes_for参数
allow_destroy: 是否能删除关联对象， 默认设为false

reject_if: 检查关联属性xxx_attributes值， 返回true时属性会被忽略。接受Proc，Symbol和:all_blank。

limit: has_many关系时起作用， 能处理的关联对象的最大数量。接受Proc, Symbol和数字， proc和symbol关联的方法需要返回数字。

update_only: has_one关系时起作用， 是否更新关联对象，默认设置为false。设置为true时， 不管参数中是否有id，都更新关联对象。

### 更新
如何参数中有'id'或者:id则更新否则创建新对象关联， 通过当前类的Reflection对象上调用assign_attributes(attributes)来更新。

其中， limit对has_many一次处理多少对象做出限制， 如果超出范围抛出TooManyRecordsy异常；update_only设为true时has_one关联都会更新关联对象。

```
params = { article: { author: { id: '2', name: 'Sam' }} }
article.update params[:article]

params = { article: { comments: [{ id: '1', content: '...' }, { id: '2', content: '...' }}] }
article.update params[:article]
```

### 删除
当allow_destroy设置为true是， 就可以接受一个_destroy参数，该参数值为true时并且id存在， 关联对象就会删除；如果没有给出id， 就会忽略。
删除方式是通过调用autosave关联的mark_for_destruction来标记关联对象。因此所有删除均要在当前对象save之后起效。

```
params = { article: { author: { id: '2', name: 'Sam'， _destroy: '1' }} }
article.update params[:article]
article.save
```
