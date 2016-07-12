title: "聊聊PHP中的范围解析操作符"
date: 2016-07-12 23:57:23
tags: PHP
category: [PHP]
---

> 在PHP中，范围解析操作符可以用于访问静态成员，类常量，还可以用于覆盖类中的属性和方法。

这是手册中，对于范围解析符的概论。

在日常工作中，我们经常会用在例如：

1. 调用类常量: `SomeCls::THE_CONST`，和`public`的静态变量 `SomeCls::$VAR`
2. 调用静态方法: `SomeCls::staticMethod()`

当然，在于类的内部，我们可以使用 `self`，`parent` 和 `static` 关键字来进行访问。

这里，我要强调的是一种比较常见但是通常我们会忽略的一种用法。

我们经常会看到这样的代码：

```
class Parent
{
	public function __construct(){}
}

class Child extends Parent
{
	public function __construct()
	{
		parent::__construct();
		//some other logic
	}
}
```

我们都知道，在 `Child` 的构造方法中 `parent::__construct()` 是主动调用父类的构造方法。

但是，为什么会需要我们主动去调用呢？

> 当一个子类覆盖父类的方法时，PHP不会调用父类已被覆盖的方法。是否调用父类的方法取决于子类。
> 这种机制也作用于构造函数和析构函数，重载以及魔术方法


所以，当见到诸如 `parent::__construct`，`parent::__get` ... 等调用时我们就知道其中的原由了。

为什么会写这篇文章？因为有一天, 我因为看到下面的代码而蒙逼了...当我知道上面的原理后才明白过来。

```
class BasePage
{
	protected function renderValue($key, $value)
	{
		//...
	}
}

class Page extends BasePage
{
	public function renderValue($key, $value)
	{
		$key = $key + $value;
		parent::renderValue($key, $value);
	}
}
```

所以文档熟烂于心才是硬道理！

参考资料：

* http://php.net/manual/en/language.oop5.paamayim-nekudotayim.php
