title: "今日笔记"
date: 2013-02-21 23:55:11
tags: PHP
category: [PHP, daliy]
---

__PHP获取当前类名、函数名、方法名__

* __CLASS__获取当前类名
* __FUNCTION__ 获取当前函数名
* __METHOD__ 获取当前方法名

_(返回区分大小写)_


### 关于PHP中的函数

* 函数名是大小写无关的
```php
function Test() {
	echo 'test';
}

test();// print test

class Test
{
	public function Say()
	{
		echo 'say something';
	}
}
$t = new Test;
$t->say(); //print say something
```

* 函数无需在调用之前被定义，但有两个特例

	* 当一个函数是有条件被定义时，其定义必须在调用之前先处理。
	* 函数中的函数

> PHP 中的所有函数和类都具有全局作用域，可以定义在一个函数之内而在之外调用，反之亦然。

关于函数中的函数例子：
```php
function foo()
{
	function bar()
	{
		echo 'I will exist until foo() is called!';
	}
}

/* 先在还不能调用bar()，因为还不存在 */
foo();

/*因为foo()函数的执行使bar()变为已定义函数*/
bar();
```