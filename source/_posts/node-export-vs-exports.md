title: "export vs exports"
date: 2015-04-22 00:27:01
tags: NodeJS
category: [NodeJS, javascript]
---

参考文章：https://cnodejs.org/topic/5231a630101e574521e45ef8

### 总结

我们只需知道三点即可知道 exports 和 module.exports 的区别了：

* exports 是指向的 module.exports 的引用
* module.exports 初始值为一个空对象 {}，所以 exports 初始值也是 {}
* require() 返回的是 module.exports 而不是 exports