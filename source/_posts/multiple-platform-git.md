title: 多平台Git配置
date: 2016-01-11 21:57:21
tags: [Git, github]
category: [Git, github]
---

我们通常会混迹多个git平台, 这时我们需要配置能够在多个平台通用的ssh.

## 设置用户名和邮箱

	$git config --global user.name "yourname"
	$git config --global user.email "your@example.com"

## 生成ssh-key

> ssh-key保存在 `~/.ssh` 目录中

	$cd ~/.ssh
	$ssh-keygen -t rsa -C "your@example.com"

注意在生成key file的时候可以针对不同的平台来命名, 例如：`id_github`, 这将会在接下的来的配置中用到.

生成**Key**之后就是将 `xx.pub` 的内容配置到平台的SSH配置中.

## 配置文件

在 `~/.ssh` 目录下新建 `config` 文件并编辑：

	Host git.coding.net
		User your@example.com
		IdentityFile ~/.ssh/coding_rsa
	Host github.com
		User your@example.com
		IdentityFile ~/.ssh/github_rsa

## 测试是否连接成功

	$ssh -T git@git.coding.net
	$ssh -T git@github.com
