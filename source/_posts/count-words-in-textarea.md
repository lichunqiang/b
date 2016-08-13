title: "修复在对textarea进行字数统计时的一个bug"
date: 2016-08-12 20:50:22
tags: [javascript]
category: [javascript]
-------

## 起因

一天一位[vux](https://github.com/airyland/vux)使用提交了这么一个bug: [x-textarea字数统计异常](https://github.com/airyland/vux/issues/420).

具体就是在当在textarea中输入回车之后，就会导致字数统计异常, 而对textarea设置了`maxlength`属性, 这样就会导致如果在textarea中输入过回车，就会导致统计字数还未达上限，但是已经不能输入了.

首先我们来看一下字数统计功能是怎么做的：

```
count() {
	return this.value.length
}
```

代码中使用了字符串 `length` 来做字数统计, 但是为什么当有回车时, 这样会导致数字统计异常呢? 是不是只有在textarea中的表现是这样的呢?

## 验证问题

首先, 我验证了在input中的表现, 结果是统计表现正常.

## 发现问题原因

为什么会这样? 在遨游了「全世界」之后找到了答案：

http://www.bennadel.com/blog/161-ask-ben-javascript-replace-and-multiple-lines-line-breaks.htm

> Textarea values might have the character combos "\n\r" in the text box, but once they are pulled into Javascript, "\n\r" becomes JUST "\n".


## 解决问题

导致问题的原因已经找出来了，那么接下来就是解决问题.

上面我们知道, 在输入一个回车后都会导致少统计一个数字, 那么我们可以在进行统计的时候做下处理：

```
count () {
	let len = this.value.replace('\n', 'aa').length
	return len > this.max ? this.max : len
}
```

是不是很tricky? 但是却是能解决问题的!

