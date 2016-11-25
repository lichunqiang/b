title: "cachegrind.out Filled My Drive"
date: 2016-11-25 10:50:22
tags: [vagrant, linux]
category: [vagrant, linux]
-------

在使用 vagrant 开发过程中, 突然遇到磁盘被沾满, 导致数据库无法写入.

```
文件系统                 容量  已用  可用 已用% 挂载点
/dev/mapper/centos-root   39G   39G  136K  100% /
devtmpfs                 911M     0  911M    0% /dev
tmpfs                    921M     0  921M    0% /dev/shm
tmpfs                    921M  8.4M  912M    1% /run
tmpfs                    921M     0  921M    0% /sys/fs/cgroup
/dev/sda1                497M  125M  373M   25% /boot
tmpfs                    185M     0  185M    0% /run/user/1000
```

有足足的__39G__为啥就满了呢?

通过 `sudo du -h -d 1` 在home查看发现:

```
32G     ./tmp
3.6G    ./var
2.3G    ./usr
1.9G    ./home
```

`/tmp` 文件足足占了32G了, 可想而知磁盘空间沾满的罪魁祸首在这儿.

进入文件查看, 里面大部分是 `cachegrind.out.*` 文件, 有的单个文件就达到了26个G.

经过查询, 才知道这些文件是Xdebug产生的文件. 好吧, 既然知道了这些文件的产生以及用途, 删掉就好了!

```
$ sudo find /tmp/  -name 'cachegrind*' -exec rm '{}' \;
```

再来看下磁盘占用：

```
文件系统                 容量  已用  可用 已用% 挂载点
/dev/mapper/centos-root   39G  6.9G   32G   18% /
devtmpfs                 911M     0  911M    0% /dev
tmpfs                    921M     0  921M    0% /dev/shm
tmpfs                    921M  8.4M  912M    1% /run
tmpfs                    921M     0  921M    0% /sys/fs/cgroup
/dev/sda1                497M  125M  373M   25% /boot
tmpfs                    185M     0  185M    0% /run/user/1000
```

最后可以将清理命令加入crontab来定时执行清理.


--EOF--
