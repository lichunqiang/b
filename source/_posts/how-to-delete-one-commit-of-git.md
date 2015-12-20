title: "删除一次错误的提交"
date: 2015-12-20 20:17:39
tags: git
category: git
---


**注意**: `git reset --hard`会彻底删除你的变动,你可以在这之前使用 `git stash` 来保存变动。

```
git reset --hard HEAD~1
```

`HEAD~1`代表当前提交之前的一次提交.

或者使用 `git log` 找到你想revert的commit_id：

```
git reset --hard <sha1-commit-id>
```

如果你已经提交到了远程仓库, 可以通过以下命令来抹掉提交：

```
git push origin HEAD --force
```