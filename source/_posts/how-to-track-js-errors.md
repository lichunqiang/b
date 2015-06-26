title: "怎样收集js报错"
date: 2015-05-13 22:02:06
tags: [javascript, Yii2]
category: [PHP, Yii2]
---

项目上线运行中，由于这样那样奇葩的浏览器的存在，导致我们的js代码存在一定的兼容性问题。那么可否收集到这些问题呢？

答案是肯定的。我们可以基于 `window.onerror` 事件来收集并上报前段js代码在实际运行中产生的错误，以帮助我们排查解决。

在W3C规范里，window.onerror是html5新定义的事件，但实际上，window.onerror从IE6开始就被支持了，而chrome、firefox、safari、opera，目前也都已经支持该事件。

```javascript
//收集js报错信息并上报
window.onerror = function(msg, url, line, column, error) {
	var _e = encodeURIComponent,
	content = '/site/jslog?message=' + _e(msg) +
	  '&url=' + _e(url || window.location.href) + '&line=' + _e(line) +
	  (error && error.stack ? '&stack=' + _e(error.stack) : '') +
	  (column ? '&column=' + _e(column) : '') +
	  '&ua=' + window.navigator.userAgent.replace(/[\:,;]/g,"|");
	(new Image()).src = content;
}
```

服务端:

```php
function actionJslog()
{
	$response = Yii::$app->getResponse();
	//这是必须的，我们要将header设置为image
    $response->format = Response::FORMAT_RAW;
	//write the log data
	$headers = $response->getHeaders();
	$headers->set('Content-Type', 'image/png');
	return $response;
}
```

这段代码需要放在head中并且位于所有script之前！

这样当客户端发生js报错时就会上报到服务器端，在服务器端我只需要将提交过来的信息进行保存就可以了。这样我们就可以根据收集到的信息来解决问题。

这里有几个点需要注意，一个是日志收集接口暴露在外，最好增加安全措施，比如设置token。

另外，脚本部署的时机、位置，以及日志过滤等条件，也需要根据具体情况进行设定。

__教训__:

在代码中我们经常使用 `console.log` 来打印代码，但这存在问题的，某些浏览器是不支持该方法的！

这就会产生错误，使代码无法正常运行！有两张方法可以解决：

1. 利用工具来消除所有的打印调试代码

2. 提供兼容代码「置于head中所有script之前」

```javascript
window.console = window.console || (function() {
	var _c = {};
	_c.log = _c.warning = _c.error = _c.info .. = function() {};
	return _c;
})();
```