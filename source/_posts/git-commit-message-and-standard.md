title: "设置Git提交信息模板"
date: 2019-01-10 16:53:42
tags: [Git, git]
category: [Git, git]
---
#### 配置 commit.template

如果把此项指定为你系统上的一个文件，当你提交的时候， Git 会默认使用该文件定义的内容。 例如：你创建了一个模板文件`$HOME/.gitmessage.txt`，它看起来像这样：

```
subject line

what happened

[ticket: X]
```

设置`commit.template`，当运行`git commit`时， Git 会在你的编辑器中显示以上的内容， 设置`commit.template`如下：

```bash
$ git config --global commit.template $HOME/.gitmessage.txt
$ git commit
```

#### 社区规范

https://www.conventionalcommits.org/en/v1.0.0-beta.2/

通用模板：

```
<type>[optional scope]: <description>

[optional body]

[optional footer]
```

###### types

- fix: 修复代码提交
- feat: 引入新的功能提交
- chore，docs，style，refactor，perf，test，improvement

###### scope

限定当前提交类型所涉及的范围：`feat(parser): add ability to parse arrays.`

##### Examples

提交信息含有描述和break change

```
feat: allow provided config object to extend other configs

BREAKING CHANGE: `extends` key in config file is now used for extending other config files
```

不含提交信息正文

```
docs: correct spelling of CHANGELOG
```

包含范围信息

```
feat(lang): added polish language
```

修复bug

```
fix: minor typos in code

see the issue for details on the typos fixed

fixes issue #12
```
