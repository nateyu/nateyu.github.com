---
layout: post
title: "Git中的撤销操作"
date: 2014-07-20 23:31
comments: true
categories: git
---

在使用Git的时候，有时候我们需要对已有的修改进行撤销操作， 接下来介绍一些基本的撤消操作相关的命令。

###git commit --amend修改最后一次提交
这个命名用于修改你最后的一次提交。如果在commit之后发现自己写错说明，或者漏掉某个文件，这个时候你可以用--amend重新提交一次。--amend使用与你刚才提交相同的快照进行修改，就相当于手动修改刚才的提交。
当你执行--amend时，git会启动你的编辑器，你可以修改刚才的注释，如果有遗忘的文件没有提交，可以先缓存然后在执行--amend。

```ruby
git add test.rb
git commit --amend
```
###撤销已经缓存的文件
--amend命令是修改已经提交的内容。当文件不小心被缓存之后，特别当你使用git add .之后，发现很多文件不是我们要提交的。这个时候你可以用：

```ruby
git reset HEAD <file>
```

取消文件的缓存，但是文件修改的内容不会撤销。

###撤销已经修改的文件内容
如果我们需要撤销已经修改的内容，就需要使用, git checkout filename

```ruby
git checkout test.rb
```

现在test.rb所有的修改都被撤回到当前分支HEAD时的修改了。

###回滚当前版本
当我们回滚到历史某一个版本时，我们可以使用git reset命名。一般我们当前指针，也就是branch_name已经HEAD所指向的那个节点，是我们正在使用的版本。如果要回到当前版本之前某个历史版本，就可以使用git reset [--hard|soft|mixed|merge|keep] [<commit>或HEAD]

1. --hard, 重置当前工作目录和索引。也就是说，你本地修改内容都会丢弃，并且HEAD指向<commit>。
2. --soft, 当前工作目录和和索引不会改变，仅仅将HEAD指向<commit>。也就是说所有的修改，缓存都不变，和你没有reset之前是一样的。
3. --mixed， 当前工作目录，但是或重置索引。也就是说，你的修改内容不会变化，但是会取消缓存。
4. --merge, --keep用的不多,不说了。

其中<commit>默认是最后一次commit, 模式默认是mixed。

###撤销某次提交
当你发现某次提及有问题，需要撤销时可以使用git revert [<commit>或者HEAD]。revert可以说是commit的逆操作， 你commit做了哪些操作，revert就相反的执行; commit修改文件，添加文件，revert就删除文件，并且撤销修改，然后产生一次提交。

```
git revert 226d852d0c0
```
226d852d0c0提交就会被一次新的提交重置掉。

它与reset --hard的区别是，reset时历史上回朔，在这个历史区间内的所有提交都将的丢弃；而revert则是正对某次提交进行一次逆向操作。

