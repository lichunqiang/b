title: "Install Golang on Centos"
date: 2016-05-27 18:57:23
tags: Golang
category: [Golang]
---


## Download

首先确定操作系统位数, 可执行：

```
$ uname -m
```

如果输出为 `x86_64` 的话代表64位操作系统，如果为 `i386` 则为32为系统。


根据确定的系统位数下载对应版本的安装包，我的是系统是64，所以

```
$ wget https://storage.googleapis.com/golang/go1.6.2.linux-amd64.tar.gz
```

解压到安装目录, 我把go安装到 `/usr/local/go` 中，执行：

```
$ tar zxvf go1.6.2.linux-amd64.tar.gz
$ mv go /usr/local
```

## 配置环境变量


添加 `GOROOT` 环境变量

```
$ export GOROOT="/usr/local/go"
```

可以通过执行 `env` 或者 `echo $GOROOT` 来确认环境变量是否正常设置.

设置PATH

```
$ export PATH=$PATH:/usr/local/go/bin
```

## 测试是否安装成功

```
$ go version
```

输出 `go version go1.6.2 linux/amd64` 表示golang已经成功安装！


## GOPATH

剩下一步比较重要的就是设置 `GOPATH`, 详情可查看[资料](https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/01.2.md)

例如我将GOPATH设置为 `/home/go`  为我的工作空间：

```
$ export GOPATH="/home/go"
```

`/home/go`的目录结构为：

```
-- go
  --bin
  --pkg
  --src
```

有关目录结构的介绍可以参考：https://github.com/astaxie/build-web-application-with-golang/blob/master/zh/01.2.md


至此Golang已经成功安装！

## 小知识

直接在命令行运行 `$ export PATH=$PATH:/usr/local/go`， 只会在当前会话有效，登出或者注销系统后，PATH的设置就会失效。

如果想永久保存设置，则需要修改`/etc/profile`，在末尾加上(如果之前没有添加过的话):

```
PATH=$PATH:/usr/local/go
export PATH
GOPATH=/home/go
export GOPATH
```
然后执行

```
$source /etc/profile
```

之后可以通过 `echo $PATH` 来查看设置是否生效。
