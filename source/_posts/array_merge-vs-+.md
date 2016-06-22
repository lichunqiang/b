title: "array_merge 和 + 之间的区别"
date: 2016-06-22 21:57:23
tags: PHP
category: [PHP]
---

首先，两种方法都能够实现两个或者多个数组之间的合并。但是，两个方法还是有差别的。

+号操作符返回的是右边的数组附加到左边的结果数组。如果存在一个键值都存在两个数组中，那么左边的将会使用，不会被右边的数据给覆盖。

举个栗子： 

```
$a = ['a' => 'apple', 'b' => 'banana'];

$b = ['a' => 'orange', 'c' => 'cherry'];

$c = $a + $b;

var_dump($c);
```

将会输出：

```
array(3) {
  ["a"]=>
  string(5) "apple"
  ["b"]=>
  string(6) "banana"
  ["c"]=>
  string(6) "cherry"
}
```

那么如果存在数字键值，又会有是什么表现方式呢？稍微改动下上一个栗子：

```
$a = ['a' => 'apple', 'b' => 'banana', 1];

$b = ['a' => 'orange', 'c' => 'cherry', 2];

$c = $a + $b;

var_dump($c);
```

将会输出：

```
array(4) {
  ["a"]=>
  string(5) "apple"
  ["b"]=>
  string(6) "banana"
  [0]=>
  int(1)
  ["c"]=>
  string(6) "cherry"
}
```

可以看到和字符串表现一致。


再来测试下上面的代码， array_merge 的表现是怎样的：


```
$a = ['a' => 'apple', 'b' => 'banana'];

$b = ['a' => 'orange', 'c' => 'cherry'];

$c = array_merge($a, $b);

var_dump($c);
```
输出结果为：

```
array(3) {
  ["a"]=>
  string(6) "orange"
  ["b"]=>
  string(6) "banana"
  ["c"]=>
  string(6) "cherry"
}
```

那么正如文档中所说的，将会对相同的键值进行覆盖！那么对于数字键值是不是也是同样的处理呢？再看：


```
$a = ['a' => 'apple', 'b' => 'banana', 1];

$b = ['a' => 'orange', 'c' => 'cherry', 2];

$c = $a + $b;

var_dump($c);
```
输出结果：

```
array(5) {
  ["a"]=>
  string(6) "orange"
  ["b"]=>
  string(6) "banana"
  [0]=>
  int(1)
  ["c"]=>
  string(6) "cherry"
  [1]=>
  int(2)
}
```

可以看到，并没有进行覆盖!由此可以得出结论，当两个数组中含有数值健的时候，会进行附加，并且键值会从0开始重建索引键值。


参看资料：

* http://stackoverflow.com/questions/7059721/array-merge-versus
* http://php.net/manual/en/language.operators.array.php
* http://php.net/manual/en/function.array-merge.php
