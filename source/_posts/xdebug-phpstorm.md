title: "在phpstorm中使用xdebug"
date: 2016-01-26 11:17:39
tags: [xdebug, phpstorm]
category: [xdebug, phpstorm]
---

## 开启xdebug

在`php.ini`中配置`xdebug`如下：

  [xdebug]
  xdebug.remote_enable = On
  xdebug.remote_handler = dbgp
  xdebug.remote_host= localhost
  xdebug.remote_port = 9000
  xdebug.idekey = PHPSTORM
  xdebug.profiler_enable = 1
  
重启服务之后在`phpinfo`中查看是否已经开启`xdebug`.

## 配置phpstorm

1. 配置xdebug端口号
  
  在 File > Settings > Langauges&Frameworks 中找到 `PHP` 选择`Debug`选项卡,找到`xdebug`配置项，确认端口号是上面设置的`9000`
  
2. 配置DBGp Proxy
  
    点击`DBGp Proxy`选项卡，在里面设置:
    
      IDE Key: PHPSTORM
      Host:   localhost
      Port:   9000

3. 添加 servers

  找到 `Servers`选项卡,点击添加按钮添加一个配置信息。例如想调试部署好的虚拟地址：`www.app.local`,那么你需要在`Host`中填上该地址。
  注意`Port`需要和你实际配置的端口要一直。另外保证`Debugger`为`Xdebug`
  
  
  这里主要记录下如何配合浏览器插件`Xdebug helper`来进行调试
  
## 安装浏览器插件`Xdebug helper`

  安装成功之后，在选线卡将你配置的domain进行添加。
  
  
## 在postman中使用xdebug

1. Configure your xdebug (by editing php.ini) to attempt to debug every php script (xdebug.remote_autostart = 1)

2. Add xdebug session start parameter to the actual URL (XDEBUG_SESSION_START={{KEY}} -- http://xdebug.org/docs/remote ), for example: ?XDEBUG_SESSION_START=PHPSTORM

3. Pass xdebug cookie as one of the headers (the one which is set by bookmarklet or browser extension, for example)
  
  参考文章:
  
  * http://my.oschina.net/u/248080/blog/351455
  * http://stackoverflow.com/questions/19139803/xdebug-a-restful-server-using-phpstorm-or-postman
  
