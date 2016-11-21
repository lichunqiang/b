title: "升级Https中遇到的一个小坑"
date: 2016-11-19 13:50:22
tags: [nginx, https]
category: [nginx, https]
-------

在全站升级Https的过程中需要对websocket服务进行https化。

之前, 使用workerman提供服务, 并采用暴露端口的方式供系统通过IP形式访问。

采用Https后, 采用的方案是使用nginx做一层反向代理, 这样可以取消之前websocket暴露外网的端口.

## 踩坑


### 配置

为websocket配置特定的后缀, 有nginx进行反向代理：

```
location = /socket.io/ {
    proxy_pass http://127.0.0.1:2120;
}
```

`127.0.0.1:2120` 为 workerman 启动的websocket地址.

### 测试效果

__WTF__ , 结果并没有按照预期的完全无误. 在控制台中得到如下错误提示：

```
wss://.../socket.io/?EIO=2&transport=websocket&sid=p3af7ZNfvogtq6tAAAG0'

failed: Error during WebSocket handshake: Unexpected response code: 400.
```

然而, 开始并没有去查看导致这个错误的具体原因是什么, 而是直接进行了测试, 测试的结果是

系统能够正常运行.


## 出坑

当然, 仅仅能够正常运行并不能够证明什么, 事实上, 错误仍然存在. 之后google了一番, 在 socket.io 库的

issue里找到了解决方案：

https://github.com/socketio/socket.io/issues/1942#issuecomment-82352072

尝试：

```
location = /socket.io/ {
    proxy_pass http://127.0.0.1:2120;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header Host $host;
}
```

再次测试, 错误消失！



1. `proxy_http_version 1.1;`

> The first line tells Nginx to use HTTP/1.1 when communicating to the Node backend, which is required for WebSockets.

告诉nginx使用 `HTTP/1.1` 协议和后端的websocket服务通信

2. `proxy_set_header Upgrade $http_upgrade` & `proxy_set_header Connection "upgrade"`

>  The next two tell Nginx to respond to the Upgrade request which is initiated over HTTP by the browser when it wants to use a WebSocket.

这两个告诉Nginx应对升级请求发起HTTP上的浏览器当它想要使用一个WebSocket。



参考: https://chrislea.com/2013/02/23/proxying-websockets-with-nginx/

--eof--
