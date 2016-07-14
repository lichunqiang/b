title: "beanstalkd在centos7下部署小记"
date: 2016-07-14 09:26:12
tags: [beanstalkd, 消息队列]
category: [beanstalkd, 消息队列]
-------

1. 下载源代码到本地

```
$ wget https://github.com/kr/beanstalkd/archive/v1.10.tar.gz
```

2. 解压安装

```
$ tar zxvf v1.10.tar.gz && rm -rf v1.10.tar.gz
$ mv beanstalkd-1.10/ /usr/local/beanstalkd
$ cd /usr/local/beanstalkd
$ make && make install
```

3. 加入服务以及开机启动

```
$ cp /usr/local/beanstalkd/adm/systemd/beanstalkd.service /usr/lib/systemd/system
$ systemctl enable beanstalkd.service
$ systemctl start beanstalkd.service
```

> 默认beanstalkd开始了binlog, 所以确保默认的目录存在 `mkdir -p /var/lib/beanstalkd/binlog`

参考资料:

* https://segmentfault.com/a/1190000002784775
