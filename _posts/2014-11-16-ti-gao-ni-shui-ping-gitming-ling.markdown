---
layout: post
title: "提高你水平git命令"
date: 2014-11-16 22:56
comments: true
categories: git
---

我们在使用Git常用的add, commit和merge等命令来处理我们日常的工作。本文介绍几个不常用，但是能给你带来极大的效率提高同时也能让你Git水平提高一个档次。

###git blame [file_name]
查看当前file_name文件中的每一行的修改记录。这个在我们想知道某一行是由谁最后一次修改时特别有用, 当你代码出了问题可以快速揪出那个坑人同伴。实用指数5星。

{% highlight ruby linenos %}

git blame Gemfile

^0a57942 (rubyrock      2012-07-31 09:46:47 +0800  1) source 'http://ruby.taobao.org'
0f727cfd (ShiningRay    2014-05-16 14:59:10 +0800  2) source 'http://boohee:boohee0690@gems.boohee.cn'
^0a57942 (rubyrock      2012-07-31 09:46:47 +0800  3) 
3556c391 (vincent       2013-10-16 19:13:20 +0800  4) gem 'rails', '3.2.14'
^0a57942 (rubyrock      2012-07-31 09:46:47 +0800  5) gem 'mysql2'
ec26bb30 (zhouguangming 2013-01-04 17:23:54 +0800  6) gem 'sqlite3'
^0a57942 (rubyrock      2012-07-31 09:46:47 +0800  7) 
2421794e (vincent       2013-10-17 14:55:53 +0800  8) # Sunspot. Full-text search engine
^0a57942 (rubyrock      2012-07-31 09:46:47 +0800  9) gem 'sunspot_rails'
d4327102 (zhouguangming 2012-07-31 08:43:59 +0800 10) gem 'sunspot_solr', '1.3.2'
45ec2a81 (vincent       2014-09-10 23:35:53 +0800 11) gem 'progress_bar', require: false

....

{% endhighlight %}

###git log --oneline, --graph, --all
我回顾历史时候常用git log。这个是按照提交顺序来排列显示你一些基本信息，你可以试试添加以下几个参数：

--oneline, 以极简的形式只显示commit的hash号和message   
--graph, 以图形树显示历史，SourceTree控们试着换换口味啦   
--all, 显示全部分支历史，不止当前分支哦   

最后，以上三个可以一起组合使用。

```ruby

* 64bb1b1 清理掉ifood星级计算和星级标签中对ifood_menu_materials和ifood_upload_menu_materials的关联
* bb9d626 去掉ifood model中ifood_menu_material, ifood_upload_menu_materials关联
* 8527d02 修改ifood_menu_material, ifood_upload_material
| * 2f4ba29 导入food_meal数据
| * 470322e Refactor: meal_time to meals_order, meal_kind to meals_kind.
| * 83afd02 增加 food_meal，用于存储食物的餐别meal_time和餐类 meal_kind.
| *   37a05a1 Merge branch 'master' of git.boohee.cn:ruby/ifood
| |\  
| | | *   ba30fa8 WIP on hotfix/remove-duplicated-models: 26f481d Merge branch 'hotfix/remove-duplicated-models' of git.
| | | |\  
| |_|/ /  
|/| | |   
| | | * 211cda6 index on hotfix/remove-duplicated-models: 26f481d Merge branch 'hotfix/remove-duplicated-models' of git.
| |_|/  
|/| |   
* | |   26f481d Merge branch 'hotfix/remove-duplicated-models' of git.boohee.cn:ruby/ifood into hotfix/remove-duplicated
|\ \ \  
| * | | 4798136 移除IfoodMenuMaterial关联
* | | | 567d5e8 移除IfoodMenuMaterial关联
|/ / /  
* | | b7ca53f remove binding.pry in qa
* | | a9bb314 Remove IFoodUnit
* | |   65a3fc5 Merge branch 'master' of git.boohee.cn:ruby/ifood into hotfix/remove-duplicated-models
|\ \ \  
| | |/  
| |/|   
| * |   2d60cef Merge branch 'master' of git.boohee.cn:ruby/ifood
| |\ \  
| * | | 7950470 Update new relic licence key
:

```

###git reflog
有时候我们迫不得已使用了git reset --hard，然后你马上意识到你丢失所有的提交。如果你还想在看看怎么办呢？那就使用git reflog吧。请记住Git是不会丢失任何一个commit信息的。

###git add -p [file_name]
设想一下，我们为一个文件写了好几个方法。但是有的还没有完全写好，我们只想提交已经写好的部分。怎么办？用git add -p filename, 可以互交是的提交一个文件的部分改动。

同时还有git add -i 也是互交试添加缓存，很好实用的。

###git rebase -i HEAD~[number_of_commits]
有没有想过一个功能可能存在多个提交，在最后上线前我们想这多个commit合并成一个。那就用rebase吧。

```
git rebase -i HEAD~4
```

将从HEAD之前的4次提交合并成一个

###git fsck --lost-found
有些时候我们的commit可能没有任何的分支名或者tag指向它，到时我们查看历史记录是找不到塔，造成丢失commit的假象。我们可以用git fsck --lost-found快速找回。

```
git fsck --lost-found
Checking object directories: 100% (256/256), done.
Checking objects: 100% (8924/8924), done.
dangling blob fc81f3f48b7d8298d9624c2f210a41d9eb8edaf8
dangling blob 6b5c1ba9a16089f04a71c4b691bc00a1c6f3310f
dangling commit 3ce4507cec2d6c0e83c263500759a55156d57876
```

###git cherry-pick
这个是我最喜欢的命令了，git cherry-pick [commit-hash], 可以将其他分支的单次commit合并到当前分支，而不是整个分支合并，也不会扰乱当前分支，酷毙了。