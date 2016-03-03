title: "git tips - 1"
date: 2016-03-03 16:53:42
tags: [git]
category: [git]
---

git tip one
===========

__git config__

`git config` 配置项由本地 > 用户 > 系统层级覆盖。

所以在本地某个仓库可以设置用户名和邮箱：

  git config user.name "light"
  git config user.email "light@qq.com"
  

__为常用命令设置快捷别名__

  git config alias.st status
  git config alias.ci commit
  
> 同样可以使用`-g` 来进行全局配置

使用：
  
  $ git st
  $ git ci
