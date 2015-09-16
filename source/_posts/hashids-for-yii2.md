title: "Hashids的yii2封装"
date: 2015-08-22 00:17:39
tags: Yii2
category: Yii2
---

### 关于Hashids

Hashids 是一个将数字生成唯一且不连续的短码的库, 被封装了好多语言版本的库。

详细： http://hashids.org/php/

### 使用

```php
$hashids = Yii::createObject([
    'class' => 'light\hashids\Hashids'
]);

$id = $hashids->encode(100123123);
$origin = $hashids->decode($id);
var_dump($id, $origin);
```

[详细](https://packagist.org/packages/light/hashids)