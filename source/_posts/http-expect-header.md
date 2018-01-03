title: "解决PHP执行cURL返回头中包含100 continue"
date: 2018-01-3 10:17:39
tags: ["HTTP", "cURL", "PHP-cURL"]
category: ["HTTP", "cURL", "PHP-cURL"]
---

当使用cURL请求服务时，会执行	：

```php
$response = curl_exec($ch);
```

通过对request/response的打印可以获取以下信息:

Request:

```html
POST /item/save HTTP/1.1
Host: services.mydomain.com
Accept: */*
Content-Length: 429
Expect: 100-continue
Content-Type: multipart/form-data
```

Response:

```
HTTP/1.1 100 Continue

HTTP/1.1 200 OK
Date: Fri, 06 Jul 2012 08:37:01 GMT
Server: Apache
Vary: Accept-Encoding,User-Agent
Content-Length: 256
Content-Type: application/json; charset=utf-8
```

通常我们在对response进行header和body的拆分的时候由于多返回了一段header，则会导致解析响应body的时候出现问题。

常见的浏览器不会发送`Expect`消息头，但是其他类型的客户端如cURL默认会这么做。

__Expect__ 是一个请求消息头，包含一个一个期望条件，表示服务器只有在满足此期望条件的情况下才能完成请求处理。

目前__HTTP__规范只规定了一个期望条件，即`Expect:100-continue`，对此服务器可以做以下回应：

- `100`如果消息头中的期望条件可以得到满足，请求可以顺利进行
- `417`（Exceptation Failed）如果服务器不能满足期望条件的话，也可以返回人任意其他(4xx)错误码

`Expect: 100-continue`即通知接收方客户端要发送一个体积可能很大的消息体，期望收到状态码为`100`(Continue)的临时回复。

服务器通过返回一个状态码为`100`（Continue）的回复告知客户端可以继续发送消息体。

所以希望解决上面的问题，我们可以主动发送一个空的`Expect`请求头即可:

```php
curl_setopt($ch, CURLOPT_HTTPHEADER, ['Expect:']);
$response = curl_exec($ch);
```

