title: frp使用之联通外界 
date: 2018-03-27 22:00:19
tags: ["frp", "socks5", "http_proxy"]
category: ["frp", "socks5", "http_proxy"]
---

## 使用场景

机器A：10.9.193.137 无法访问外网，但能与有外网访问权限的机器连接
机器B：10.9.197.104 可以外网联通，并和机器A联通

## 目的

使机器A可以访问外网(git, composer, etc...)

## 实施步骤

frp 部署宿主机器：机器B

启动frps
```
//frps.ini
[common]
bind_port = 7000

#dashboard
dashboard_port = 7500
dashboard_user = admin
dashboard_pwd = admin
```

启动frp server
```bash
./frps -c ./frps.ini >> frps.log 2>&1 &
```

启动client(frpc)
```
//frpc.ini
[common]
server_addr = 127.0.0.1
server_port = 7000

[test_static_file]
type = tcp
remote_port = 6001
plugin = static_file
# 要对外暴露的文件目录
plugin_local_path = /home/work/light/files
# 访问 url 中会被去除的前缀，保留的内容即为要访问的文件路径
plugin_strip_prefix = static
plugin_http_user = abc
plugin_http_passwd = abc

[socks5]
type = tcp
remote_port = 6002
plugin = socks5

[http_proxy]
type = tcp
remote_port = 6000
plugin = http_proxy
```

启动frp client
```bash
./frpc -c ./frpc.ini >> frpc.log 2>&1 &
```

这样就在机器B上分别部署了三个服务：

* 静态文件服务器
* socks5
* http_proxy

机器A上使用http_proxy或者socks5联通外网

```bash
export http_proxy="http://username:password@10.9.197.104:6000"
composer:
export HTTP_PROXY="http://10.9.197.104:6000"
composer install
curl:
curl --socks5 socks5://10.9.197.104:6002 https://www.baidu.com
```

```bash
yum:

vim /etc/yum.conf

proxy=http://10.9.197.104:6000
proxy_username=username
proxy_password=password
```
<img src="/images/frp-as-bridge.png" />
