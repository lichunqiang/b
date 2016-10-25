title: "开启Https之旅"
date: 2016-10-25 12:17:39
tags: [nignx, https]
category: [nignx, https]
---

> 本文记录了在centos7下使用[certbot](https://certbot.eff.org/)配置开启https 的过程

## 安装

```bash
$ sudo yum install epel-release -y
$ sudo yum install certbot -y
```

安装成功之后，可以通过执行 `certbot -h` 来查看使用帮助.

申请证书有两种验证方式，一种是__standalone___，这种验证方式虽然也可以部署后，但是以后更新证书的时候需要重启 web 服务器。

我们可以采用了第二种__webroot__方式，就是在网站根目录下生成一个文件，通过访问该文件来验证，不需要重启 web 服务器。

## 配置

```bash
$ certbot certonly --webroot -w /home/wwwroot/yoursite/web -d www.lzuer.net -d lzuer.net
```

如果希望为多个域名生成共同的证书，可以继续在面输入：`-w /home/wwwroot/othersite/web -d admin.lzuer.net`

按照提示操作，当看到下面输出后，表示证书已成功生成：

```
IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/your.domain.com/fullchain.pem. Your cert
   will expire on 20XX-09-23. To obtain a new or tweaked version of
   this certificate in the future, simply run certbot again. To
   non-interactively renew *all* of your certificates, run "certbot
   renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

## 配置nginx

nginx支持在同一个__server__支持80和443端口，这样可以实现你的网站同时兼容__http__和__https__两种模式:

```
listen  80;
listen 443 ssl;
server_name  lzuer.net www.lzuer.net;
root  /home/wwwroot/yoursite/web;
index index.php;
charset utf-8;


ssl on;
ssl_certificate /etc/letsencrypt/live/b.w.lzuer.net/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/b.w.lzuer.net/privkey.pem;
ssl_trusted_certificate /etc/letsencrypt/live/b.w.lzuer.net/chain.pem;
ssl_session_timeout 5m;
ssl_protocols TLSv1;
ssl_ciphers  HIGH:!aNULL:!MD5;
ssl_prefer_server_ciphers   on;
//...
```

保存后重启__nginx__.

> 注意, 启用https需要在安装__nginx__的时候开启__ssl module__，可以通过 `nginx -V` 来查看，如果没有开启需要重新编译并开启

测试下，访问__https__开头的网址是否能正常访问，如果能正常访问，表示配置已经生效。

> 注意, 如果发现无法访问, 可以检查下__443__这个端口是否开启，如果没有则需要开启 `iptables -I INPUT -p tcp --dport 443 -j ACCEPT`

如果希望你网站支持__https__访问，那么可以如下配置：

```
server {
  listen 80;
  server_name b.w.lzuer.net;
  return 301 https://b.w.lzuer.net$request_uri;
}
server {
  listen 443 ssl;
  server_name  b.w.lzuer.net;
  root  /home/wwwroot/yoursite/web;
  index index.php;
  charset utf-8;


  ssl on;
  ssl_certificate /etc/letsencrypt/live/b.w.lzuer.net/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/b.w.lzuer.net/privkey.pem;
  ssl_trusted_certificate /etc/letsencrypt/live/b.w.lzuer.net/chain.pem;
  ssl_session_timeout 5m;
  ssl_protocols TLSv1;
  ssl_ciphers  HIGH:!aNULL:!MD5;
  ssl_prefer_server_ciphers   on;
}
```

## 定时更新

Let’s Encrypt 只有3个月的有效期，所以我们需要定时去更新证书。

可以通过运行：`certbot renew --dry-run` 来测试自动生成是否能够正常运行。

如果通过，可以正常运行，可以加入__cron__来定时执行:

```
0 0 1 * * certbot renew --quiet
```

https://certbot.eff.org 上建议是每天不定时执行两次，来保证网站能够正常访问。

具体由于证书在没有过期之前是不会进行更新的，所以每天不定时更新两次亦可以。

参考：

* http://nginx.org/en/docs/http/configuring_https_servers.html
* https://hersface.com/page/https.html
