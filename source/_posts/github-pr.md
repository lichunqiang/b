title: "如何处理一个pull request"
date: 2016-10-10 13:50:22
tags: [Git, github]
category: [Git, github]
-------

在 [Github](https://github.com) 开源项目后, 总会有人为你添砖加瓦。当有用户为你的代码提交一个patch后，

你不仅需要在线进行code review，而且还要运行起来，确保提交的代码改动不会产生其他影响。

具体的操作步骤为：


1. 检出一个单独的分支来查看代码更改


```bash
$ git checkout -b pull-100
```

2. 拉取提交用户改动分支代码

```bash

$ git pull git://github.com/somebody/project.git master
```

> 注意, 上面是在用户提交代码分支为 `master` 的情况, 如果用户提交的改动分支非 `master`

> 那么需要改成对应的分支名

3. 在本地查看代码运行情况, 是否解决了问题


最后, 对于合并可以在本地进行:

```bash
$ git checkout master
$ git merge --no-ff pull-100
$ git push origin master
```

当然, [Github](https://github.com) 在线处理已经做得足够好, 也可以选择在线进行合并.
