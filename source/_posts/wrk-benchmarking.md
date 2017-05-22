title: "使用 wrk 来测试接口性能"
date: 2017-05-22 20:17:39
tags: [wrk, benchmarking]
category: [wrk, benchmarking]
---

## 什么是wrk

wrk是一个用来做HTTP benchmark测试的工具。可以产生显著的压力。

## 为什么是wrk

相比于Apache ab功能更为强大，可以使用lua脚本来支持更为复杂的测试场景，例如PUT请求等。

在对于Restful架构的API接口来说，测试起来更加便捷。

## 获取和安装

源码地址: [https://github.com/wg/wrk](https://github.com/wg/wrk)


```bash

$ git clone https://github.com/wg/wrk.git
$ cd wrk
$ make all
$ cp wrk /usr/bin/wrk
$ wrk -h
```

## 使用方法


### 基本使用方法

```
$ wrk -t 2 -c 50 -d 1 --latency http://bj.58.com
```

使用两个线程保持50个连接请求1秒钟，并打印延迟统计信息：

```
Running 1s test @ http://bj.58.com
  2 threads and 50 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   148.87ms  180.61ms 927.67ms   85.14%
    Req/Sec   192.47     99.38   400.00     63.16%
  Latency Distribution
     50%   65.21ms
     75%  204.23ms
     90%  355.24ms
     99%  835.83ms
  367 requests in 1.01s, 37.14MB read
Requests/sec:    364.46
Transfer/sec:     36.88MB
```

主要关注的点有：

* `Latency` 请求处理时延，上面测试结果平均延时时间为148.87ms

* `Requests/sec` 每秒处理的请求数(QPS)

上面的结果可以看到(Latency Distribution): 50%的请求延时在65.21ms

> 当然，上面的请求时间只用了1s，理论上请求的时间越长得结果更加准确

### 使用进阶

模拟POST请求

新建 `post.lua`

```
wrk.method = "POST"
wrk.body = "foo=bar&baz=qux"
wrk.headers["Content-Type"] = "application/x-www-form-urlencode"
wrk.headers["User-Agent"] = "Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.36"
```

请求:

```bash
$ wrk -t 4 -c 100 -d 30s --timeout 10 --script=post.lua --latency http://127.0.0.1/post.php
```

通过在脚本中记录日志的方式可以看到`$_POST`数据和`$_SERVER`中的字段为lua脚本中设置的值.

### 更为复杂的场景

wrk提供了几个hook函数，可以用lua来编写一些更为复杂场景下的测试需求。

* [https://github.com/wg/wrk/blob/master/SCRIPTING](https://github.com/wg/wrk/blob/master/SCRIPTING)

* [https://github.com/wg/wrk/tree/master/scripts](https://github.com/wg/wrk/tree/master/scripts)


参考资料：

* [https://github.com/wg/wrk](https://github.com/wg/wrk)
* [https://www.digitalocean.com/community/tutorials/how-to-benchmark-http-latency-with-wrk-on-ubuntu-14-04](https://www.digitalocean.com/community/tutorials/how-to-benchmark-http-latency-with-wrk-on-ubuntu-14-04)
