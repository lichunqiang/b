title: "分而治之，加快消耗"
date: 2017-09-13 9:17:39
tags: [php, queue, beanstalkd, supervisor]
category: [php, queue, beanstalkd, supervisor]
---

__场景__

任何系统的优化都会采用解耦异步的方式，将某些耗时的场景异步话，从而提升处理速度优化用户体验。例如在常见的场景：发送注册邮件，更加复杂的场景：朋友圈。

朋友圈场景中，需要处理更加复杂的业务逻辑，简单的，当用户发送一条朋友圈时，需要更新数据库「这里如果采用缓存的方式，还需要进行同步等一系列操作」，更新所有好友关系的信息流等等一系列操作。

这时就需要进行异步话，在朋友圈场景中，当前用户发出朋友圈，对于用户自己应该是最快能看到「这里常采用的方式是在APP端进行展示，异步请求服务端进行存储」，剩下的工作可以异步来完成。

__实现思路__

* 队列采用__beanstalkd__ 
* supervisor管理消费者

当用户发送朋友圈时，将tweet ID推送到__tube__ ，通过消费进程来实时消费该__tube__ 中的消息，完成异步话。



__问题__

当用户量少时，上面的方案还能正常运行。当用户量增加，朋友之间的关系更加复杂后，每条消息的处理都会非常耗时，从而大大增加了延时。

__多并行处理__

采用常用分表策略，将tweet发送到不同的tube，多开进程来处理。例如，可以根据tweet_id%5的方式，将所有的tweet根据ID取模来分配到不同的tube「还可以更多」.

这样，消费进行可以根据规则，多开进程来分别消耗。

根据上面的思路，使用supervisor来管理，设置进程数为__5__ .

```
[program:pub_tweet]
command=php yii queue/tweet %(process_num)01d
directory=/home/wwwroooot/welfare
process_name=%(program_name)s_sharding_%(process_num)s
numprocs=5
numprocs_start=0
stdout_logfile=/var/log/supervisor/%(program_name)s.log
stdout_logfile_maxbytes=10MB
redirect_stderr=true
autostart=true
autorestart=true
```

在supervisor中查看进程状态如下：

```
pub_tweet:pub_tweet_sharding_0       RUNNING   pid 22847, uptime 0:00:03
pub_tweet:pub_tweet_sharding_1       RUNNING   pid 22848, uptime 0:00:03
pub_tweet:pub_tweet_sharding_2       RUNNING   pid 22849, uptime 0:00:03
pub_tweet:pub_tweet_sharding_3       RUNNING   pid 22850, uptime 0:00:03
pub_tweet:pub_tweet_sharding_4       RUNNING   pid 22851, uptime 0:00:03
```

__ps -ef | grep tweet__

```
root     22847 22846  0 09:32 ?        00:00:00 php yii queue/tweet 0
root     22848 22846  0 09:32 ?        00:00:00 php yii queue/tweet 1
root     22849 22846  0 09:32 ?        00:00:00 php yii queue/tweet 2
root     22850 22846  0 09:32 ?        00:00:00 php yii queue/tweet 3
root     22851 22846  0 09:32 ?        00:00:00 php yii queue/tweet 4
```

可以看到已经有5个进程跑起来

那么在消费程序中就可以根据传入的当前进程num，来分别从不同的tube取出消息处理。

大致如下：

```
public function actionTweet($shared)
{
  $tube = sprint('tweet_pub_%s', $shared);
  $pheanstalk->watch($tute);
  while($job = $pheanstalk->reverse()) {
    ...
  }
}
```



