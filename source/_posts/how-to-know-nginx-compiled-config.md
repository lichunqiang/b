title: "添加nginx模块"
date: 2014-04-22 00:21:08
tags: nginx
category: [nginx]
---

#### 首先查看之前编译安装nginx的配置

```bash
$ nginx -V
```

会列出配置的参数：

```bash
configure arguments: --prefix=/usr/local/webserver/nginx --user=nginx --group=nginx --with-http_stub_status_module --with-http_ssl_module --with-http_flv_module
```

#### 重新编译增加模块

> 这个过程不会替换现有的nginx的东西，既可以在不停止服务的前提下进行替换

首先找到原来安装nginx的目录(如果已经删除，则重新现在nginx源程序)

进入该目录，然后执行：

```bash
$ ./configure --prefix=/usr/local/webserver/nginx --user=nginx --group=nginx --with-http_stub_status_module --with-http_ssl_module --with-http_flv_module --add-module path/module/

$ make

$ make install
```

保险做法：

不执行make install, 将执行make之后objs目录下的nginx替换现有的。(不要忘记对之前的进行备份)

```bash
$ ./configure --prefix=/usr/local/webserver/nginx --user=nginx --group=nginx --with-http_stub_status_module --with-http_ssl_module --with-http_flv_module --add-module path/module/

$ make

$ service nginx stop

$ cp /etc/nginx/sbin/nginx /etc/nginx/sbin/nginx.bak

$ cp objs/nginx /etc/nginx/sbin/

$ service nginx start
```

#### 测试是否安装成功