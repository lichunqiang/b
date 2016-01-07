title: "讲讲Composer配置项minimumn-stability"
date: 2015-12-29 23:57:23
tags: PHP
category: [PHP]
---

Composer 对于每个包的版本都会进行稳定性检查，而低于设置的 `minimum-stability` 最低稳定性版本的包将不会被安装。

例如，当我们在项目中想要依赖一个尚未发布稳定版本的包时候通常会：`"vendor/library": "*"`, 乍看上去并没有什么不妥, 但是当我们执行 `composer install` 或者 `composer update` 去下载包的时候通常会报错。

这是为什么呢？

通常我们的 `composer.json` 都不会显式的去设定 `minimum-stability`, 那么 `composer` 就会把他默认为 `stable`.

那么对于一个尚未发布版本的包来说, 明显是不符合的. 所以安装就不会成功.

所以有两个途径来解决：

1. 设定 `minimum-stability` 为 `dev`

显然这是简单粗暴的, 但是这种方法所面临的问题也很严重. 那就是我们去依赖某些开发版中的包, 这显然是我们所不想看到的.

2. 指定依赖包的版本, 这也是最佳的解决方案

```
composer require vendor/library=dev-master
```

所以, 当我们尝试去依赖一个开发中的包时, 推荐使用第二种方法！

另外我们还需要知道 `minimum-stability` 所支持的有：

* dev
* alpha
* beta
* RC
* stable