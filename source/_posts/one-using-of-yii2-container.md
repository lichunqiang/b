title: "Yii2 container的一个应用"
date: 2015-05-19 22:26:28
tags: [Yii2]
category: [Yii2]
---

## 起因

我们们知道,Yii内部的一些有用的类都设有默认的参数,比如 `[[yii\data\Pagination]]` 的默认每页个数是 20 个.

对于项目中的列表来说,有点多. 那么怎么才能一劳永逸的配置让所有的地方都使用我们的设定值呢? 

这时候,我们可以使用 [[Yii::$contianer]] 的来实现：

```
//这里我们设置默认个数为10, 将此置于 bootstrap.php 中
\Yii::$container->set('yii\data\Pagination', [
    'pageSize' => 10,
]);
```

这样, 我们在使用诸如 `ActiveDataProvider` 等方法时，默认每页个数就为 10 了。


## 思考

> 理论上，通过 `Yii::createObject` 来实例化的对用都会起作用。

所以，说用通过 `Yii::createObject` 创建的实例都会使用我们通过以上方法设定的默认值。因为 `ActiveDataProvider` 等中的 `pagination` 在内部是通过 `Yii::createObject` 创建的，所以设置是生效的。

因此，考虑一下两种实现方式：

```
//1. pageSize 还是默认的20
$paging = new \yii\data\Pagination;
//2. pageSize 就是设置的 10 了
$paging = Yii::createObject('yii\data\Pagination');
```

所以Yii提倡使用 `Yii::createObject` 来实例化对象，因为它能够自动解决依赖和默认值配置等好处。

## Yii::$container 是什么

```
Yii::$container = new yii\di\Container();
```

所以 `Yii::$container` 就是整个生命周期的一个依赖容器。

考虑开始的时候的代码，我们可以理解: 通过 `Yii::$container->set()` 其实就是向这个容器注入了一个类和其配置. 当调用 `Yii::createObject` 的时候会去这个容器里面找，如果存在则根据 set 的配置进行实例化。

## 推荐

关于依赖注入和依赖注入容器可以查看博文：http://www.digpage.com/di.html
