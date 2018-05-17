title: frp使用之本地服务提供外网访问 
date: 2018-02-17 21:42:08
tags: ["frp", "wechat"]
category: ["frp", "wechat"]
---

## 使用场景：

本地开发，需要提供外网访问(微信、微信小程序调试)

### 物料：
	* 一台可以外网访问的机器Server
	* 可用域名 Host

### 实施：


将 Host DNS解析到Server，如果希望使用通配符则可以添加解析为:

```
*.demo.lzuer.net -> ServerIP
```

frps部署到宿主机器：Server, 配置如下:

```
[common]
bind_port = 7000
token = &UJM8ik,

#log
log_file = ./logs/frps.log
log_level = info
log_max_days = 1

#dashboard
dashboard_port = 7500
dashboard_user = admin
dashboard_pwd = admin
#虚拟端口, 用于nginx代理
vhost_http_port = 60000
#子域名，配置后，客户端可以随便指定二级域名使用(方便多人)
subdomain_host = demo.lzuer.net
```

启动frps:
```
./frps -c ./frps.ini &
```

配置nginx代理

```
server {
	listen 80;
	server_name *.demo.lzuer.net;
	location / {
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header Host $host;
		proxy_pass http://127.0.0.1:60000;
	}
}
```

部署frpc到本地开发环境

```
[common]
server_addr = 123.56.177.147
server_port = 7000
token = &UJM8ik,

[web_qx]
type = http
subdomain = qxmugen
host_header_rewrite = qxmugen.io
local_ip = qxmugen.io
local_port = 80
remote_port = 60000

[web_lab]
type = http
subdomain = lab
host_header_rewrite = f.start.lab.io
local_ip = f.start.lab.io
local_port = 80
remote_port = 60000
subdomain 即为配置的子域名, 例如设置为 qxmugen, 则实际外网地址为 qxmugen.demo.lzuer.net
local_ip 配置为对应的本地 host_name, 因为本地开发环境使用vagrant并通过配置hosts的形式访问
```

如果不配置此项frp默认会解析到 127.0.0.1

启动frpc之后看到frps打印如下日志表示部署成功：


```
2018/05/04 10:29:28 [I] [service.go:262] client login info: ip [123.125.250.166:27629] version [0.17.0] hostname [] os [windows] arch [amd64]
2018/05/04 10:29:28 [I] [proxy.go:270] [805811e4fd83dce4] [web_qx] http proxy listen for host [qxmugen.demo.lzuer.net] location []
2018/05/04 10:29:28 [I] [control.go:327] [805811e4fd83dce4] new proxy [web_qx] success
2018/05/04 10:29:28 [I] [proxy.go:270] [805811e4fd83dce4] [web_lab] http proxy listen for host [lab.demo.lzuer.net] location []
2018/05/04 10:29:28 [I] [control.go:327] [805811e4fd83dce4] new proxy [web_lab] success
```

### 设计示意图

<img src="/images/frp-dev-wechat.png" />
