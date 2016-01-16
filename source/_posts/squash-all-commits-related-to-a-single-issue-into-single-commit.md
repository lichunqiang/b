title: 如果将多个commit精简成一个commit
date: 2016-01-11 22:22:25
tags: [Git, github]
category: [Git, github]
---

在 [Github](https://www.github.com) 上进行协作或者贡献开源项目代码时, 我们常常会对项目发起 `pull request`。

在项目的作者对发起的 `pull request` 进行审核时, 常常需要我们对修改进行进一步的修订, 以满足作者的需求。方便的是，

这一切在 [Github](https://www.github.com) 上被方便的给自动完成了，也就说，当我们在对 `patch` 分支 `push` 修改代码时，

[Github](https://www.github.com) 自动会将修改关联到上次已经发起的 `pull request`。

当这一切都**ready**之后，我们需要精简提交信息变成一条，例如： `Issue #100: bugfix for fatal error`.

	commit 6e9cd88424b5f313f7245c7a43e11063648f2dcd
	Author: lichunqiang <light-li@hotmail.com>
	Date:   Mon Jan 11 16:40:41 2016 +0800

	    tweak the description

	commit 06ce7e35206b0f8a2e5d41aab4ae21d842a7c8ab
	Author: lichunqiang <light-li@hotmail.com>
	Date:   Sun Jan 10 23:04:25 2016 +0800

	    bugfix of mailer

	commit e6e0ee8ec14624d11035a0a922617ab5dc69220f
	Author: lichunqiang <light-li@hotmail.com>
	Date:   Sun Jan 10 22:57:25 2016 +0800

	    commit message

在 git log 信息中，我们想要压缩最新的两条commit为一条

我们可以按照以下的步骤来进行：

	$ git rebase -i HEAD~2


> 2 代表我们想要压缩的commit数量。注意，在这之前，我们要保证我们的分支要和主线进行了同步。

执行命令后，在 text editor 中我们将 `pick` 替换成 `squash`, 只保留我们想保存的那条 `commit` 即可。

也可以只保留第一条的 `pick`, 将其余的替换成 `squash`。保存并退出编辑器。


**注意**，如果你已经提交到远程仓库，你需要执行:

	$ git push origin branchname --force


贴士：

你可以执行 `git commit --amend` 来修改最后一条commit的提交信息。 
