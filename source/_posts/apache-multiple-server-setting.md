title: "Apache如何配置支持多域名"
date: 2013-04-22 00:04:17
tags: note
category: [Apache, 记录]
---

### 问题

现在需要支持这种方式：
```
  light.ifenqi.com => www.ifenqi.com/site/user?name=light
```
### 解决步骤

设置vhosts如下：
```
  <VirtualHost *:80>    
      DocumentRoot "D:\project\ifenqi\frontend\web"    
      ServerName www.ifenqi.com    
      ServerAlias *.ifenqi.com    
  </VirtualHost>
```

> 注意：这项要配置在所有的地下，否则访问manager.ifenqi.com的话也会被解析到上面的服务器目录

接下来就是按照yii的urlManager，来添加规则了，详见[runtime-routing](https://github.com/yiisoft/yii2/blob/master/docs/guide/runtime-routing.md#rules-with-server-names-)
