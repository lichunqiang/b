title: "awesome-command-in-linux"
date: 2015-04-22 00:19:36
tags: linux
category: [linux, linux命令]
---

#### 在目录中查找包含文字的文件

```bash
$ grep -l -r findstring /dir
```

#### 查找文件夹

```bash
$ find -type d -name .svn
```

#### 查找目录下所有的php文件

```bash
$ find -name "*.php"
```

#### 批量删除文件

```bash
$ find -type d -name .svn | xargs rm -rf
```

#### 查看linux版本

```bash
$ cat /etc/redhat-release
```
