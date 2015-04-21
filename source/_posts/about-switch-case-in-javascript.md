title: "关于switch在js中需要注意的地方"
date: 2015-04-22 00:17:39
tags: javascript
category: javascript
---

```
var a = '1'; // switch and case use '==='
switch(a) {

	case 1:
		console.log('this is 1');
		break;
	case 2:
		console.log('this is 2');
		break;
	default:
		console.log('this is default');
}
```

switch语句后面的表达式与case语句后面的表示式，在比较运行结果时，采用的是严格相等运算符（===），而不是相等运算符（==），这意味着比较时不会发生类型转换。

Extends
===========

> switch结构不利于代码重用，往往可以用对象形式重写。

```
var o = {
    banana: function (){ return },
    apple: function (){ return },
    default: function (){ return }
};

if (o[fruit]){
    o[fruit]();
} else {
    o['default']();
}
```