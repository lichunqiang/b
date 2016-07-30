title: "一个进程管理工具-supervisor"
date: 2016-07-30 20:50:22
tags: [supervisor, 进程管理]
category: [supervisor, 进程管理]
-------

在实际的开发过程中，经常会用到守护进程来执行相应的代码。例如起一个消费beanstalkd的进程:

```
$nohup php beanstalkd-consumer.php &
```

使用这种方式，我们会发现这个进程会不明不白的「死掉」，导致消费工作无法正常进行。另外，如果一个项目中会涉及到多个守护进程来做相关工作的话，
这会导致样管理起来十分的麻烦，而且常常会担心它们会自己偷偷的「死掉」。

[supervisor](http://supervisord.org)就是这样一个工具，可以让我们很轻松愉快的对它们进行统一管理，而且省去了我们担心的某个任务会因为特殊的原因自己偷偷的挂掉。

## 安装

这里我直接使用 `yum` 进行安装：

```
$ yum install -y supervisor
```

安装成功后，会有两个可执行程序：

* __supervisord__	-- supervisor 服务守护进程
* __supervisorctl__ -- supervisor 服务控制程序.例如: `status/start/stop/restart program`

__supervisord__启动时可以通过 `-c /some/path/supervisor.conf` 来指定加载的配置文件。当然如果没有指定配置文件的位置 `supervisord` 会按照内部的寻找方式来加载配置文件

因为我是用`yum`来安装的，所以在 `/etc/supervisor.conf` 已经存在了默认的配置文件，`supervisord` 会默认加载。

## 配置

最重要的就是对`supervisor`的行为方式进行配置，通过修改`/etc/supervisord.conf`来管理.

例如，我们加入上面提到的消费进程：

```
[program:beanstalkd_comsumer]
command=/usr/local/php/bin/php /path/to/beanstalkd-consumer.php //此项指定要执行的命令
```

默认的，`supervisor`就自动管理进程挂掉自动重启等，我们来看下比较全的配置信息：

```
;[program:theprogramname]
;command=/bin/cat              ; the program (relative uses PATH, can take args)
;process_name=%(program_name)s ; process_name expr (default %(program_name)s)
;numprocs=1                    ; number of processes copies to start (def 1)
;directory=/tmp                ; directory to cwd to before exec (def no cwd)
;umask=022                     ; umask for process (default None)
;priority=999                  ; the relative start priority (default 999)
;autostart=true                ; start at supervisord start (default: true)
;autorestart=true              ; retstart at unexpected quit (default: true)
;startsecs=10                  ; number of secs prog must stay running (def. 1)
;startretries=3                ; max # of serial start failures (default 3)
;exitcodes=0,2                 ; 'expected' exit codes for process (default 0,2)
;stopsignal=QUIT               ; signal used to kill process (default TERM)
;stopwaitsecs=10               ; max num secs to wait b4 SIGKILL (default 10)
;user=chrism                   ; setuid to this UNIX account to run the program
;redirect_stderr=true          ; redirect proc stderr to stdout (default false)
;stdout_logfile=/a/path        ; stdout log path, NONE for none; default AUTO
;stdout_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stdout_logfile_backups=10     ; # of stdout logfile backups (default 10)
;stdout_capture_maxbytes=1MB   ; number of bytes in 'capturemode' (default 0)
;stdout_events_enabled=false   ; emit events on stdout writes (default false)
;stderr_logfile=/a/path        ; stderr log path, NONE for none; default AUTO
;stderr_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stderr_logfile_backups=10     ; # of stderr logfile backups (default 10)
;stderr_capture_maxbytes=1MB   ; number of bytes in 'capturemode' (default 0)
;stderr_events_enabled=false   ; emit events on stderr writes (default false)
;environment=A=1,B=2           ; process environment additions (def no adds)
;serverurl=AUTO                ; override serverurl computation (childutils)
```

## 启动

启动`supervisord`来启动和管理所有的进程：

```
$ /usr/bin/supervisord
```

启动之后我们可以通过`supervisor`的另外一个工具来查看所有配置的进程状态:

```
$supervisorctl status
```

```
beanstalkd_comsumer           RUNNING   pid 26995, uptime 4:09:54
```
