title: "配置pecl代理解决安装问题"
date: 2019-01-21 10:17:39
tags: [PHP, pecl, PHP-Extension]
category: [PHP, pecl, PHP-Extension]
---

无路在配置docker的PHP环境或者是在服务器上使用pecl安装PHP扩展时，都会遇到连接[pecl.php.net](https://pecl.php.net)连接不上的问题，导致安装包无法下载.

可以通过增加代理的方式实现翻墙下载：

Docker环境配置如下：

```dockerfile
RUN pear config-set http_proxy http://123.56.177.141:6000 \	
	RUN pecl install redis
```

> 这需要将代理设置和pecl安装扩展在同一layer执行才能生效，具体可到docker了解layer

__http_proxy__ 需要自己去通过搭建ss或者部署frp到可联通的服务器。
