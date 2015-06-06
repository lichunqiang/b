title: "redis 命令纪实"
date: 2015-05-31 22:44:42
tags: [Yii2, redis]
category: [Yii2, redis]
---

前提：
```
$redis = Yii::$app->redis;
```

### keys

```
//支持正则 h?llo [hello, hallo], h*llo [hello, heeeello]
$redis->keys('h?llo');
$redis->keys('*');
```

### set

```
$redis->set('website', 'www.google.com');
```

### exists
```
$redis->set('website', 'www.google.com');
//存在返回1，不存在返回0
$redis->exists('website');
$redis->del('website');
$redis->exists('website');
```

```
$redis->set('website', 'www.google.com');
```


```

//删除一个key，成功返回1，失败或不存在返回0
$reis->del('website');
//删除多个key, 返回成功删除的个数
$redis->del('website1', 'website2', ...);
```
