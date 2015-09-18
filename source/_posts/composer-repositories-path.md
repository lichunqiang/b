title: "Composer之path"
date: 2015-09-18 00:17:39
tags: PHP
category: [PHP, Composer]
---

> 本文讲讲用composer来进行本地开发包,和本地公用包的使用.

## 概要

当我们构建一个本地的类库时, 当并不想推送到[packagist](https://packagist.org/), 我们可以使用`repositories > path`

来处理。

以下目录结构为例：

```
- apps
\_ my-app   		-- 当前项目开发目录
  \_ composer.json
- packages			-- 本地共有库, 存放正在和已经开发好的类库
\_ my-package       -- 一个叫 my-package 的类库 
  \_ composer.json

```

现在我们要在 `my-app` 中依赖 `my-package`, 那么现在我们可以：

1. 配置 `my-app/composer.json`

```
{
	//引入依赖
	"require": {
		"myrepositry/package1": "*@dev"
	},
	//配置 repositories
	"repositories": [

		{"type": "path", "url": "../../packages/my-package"}
	]	
}
```

2. composer update

接下来就是在 `my-app` 目录下执行 `composer update`, 来使之前的配置生效!

执行成功后, 我们可以到`my-app/vendor` 目录下查看, 我们可以看到该类库已经加入到 `my-app/vendor` 了！

之后在 `my-package` 任何改动后我们需要在 `my-app` 中执行 `composer remove myrepositry/package1` 来移除,

然后在require进来.