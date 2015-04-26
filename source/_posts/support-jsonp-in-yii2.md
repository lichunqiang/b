title: "如何让Yii2支持jsonp"
date: 2015-04-22 23:47:24
tags: Yii2
category: [php, Yii2]
---

## 什么是JSONP

JSONP是一个非官方的协议，它允许在服务器端集成Script tags返回至客户端，通过javascript callback的形式实现跨域访问（这仅仅是JSONP简单的实现形式）

> 具体可以参看下玉伯大神的一篇文章[聊聊 JSONP 的 P](https://github.com/lifesinger/lifesinger.github.com/issues/118)

## 怎么发送JSONP请求

这里我使用jQuery的 `getJSON` 方法：

```
$.getJSON('http://www.b.com/api/users?callback=?', function(data) {
	console.log(data)
});
```

这里jQuery一切都为我们做好了,只需要加上参数 `callback=?`！

## Yii接受处理并返回

先上代码：

```
Yii::$app->getResponse()->format = Response::FORMAT_JSONP;
$user_count = (new Query)->from('users')->count();

return [
    'data' => [
        'errcode' => 0,
        'data' => ['count' => $user_count],
    ],
    'callback' => Yii::$app->getRequest()->get('callback'),
];
```

首先，我们需要在 action 中指定 Response 的格式为 FORMAT_JSONP.

当设置 Response 的格式为 JSONP 后，同 FORMAT 设置为JSON 我们需要返回 _数组数据_

不同点是这个数组需要有两个关键的Key，一个是data，另外一个是 callback。

* data 的内容就是回调中接收到的实际数据
* callback 是前端发送的参数中的方法名

我们来看下Yii的源代码：

位于 [[yii\web\JsonResponseFormatter]]

```
/**
 * Formats response data in JSONP format.
 * @param Response $response
 */
protected function formatJsonp($response)
{
    $response->getHeaders()->set('Content-Type', 'application/javascript; charset=UTF-8');
    if (is_array($response->data) && isset($response->data['data'], $response->data['callback'])) {
        $response->content = sprintf('%s(%s);', $response->data['callback'], Json::encode($response->data['data']));
    } elseif ($response->data !== null) {
        $response->content = '';
        Yii::warning("The 'jsonp' response requires that the data be an array consisting of both 'data' and 'callback' elements.", __METHOD__);
    }
}
```

可以看到，Yii做的处理有：

* 设置HEADER的Content-Type为 application/javascript
* 判断Response::data 是否为数组且设置了 data 和 callback 两个参数
* 组装JSONP返回