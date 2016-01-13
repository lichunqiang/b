title: "关于switch在js中需要注意的地方"
date: 2016-01-13 16:17:39
tags: [nginx, seo]
category: [nginx, seo]
---

`www.site.com` `site.com` 在配置nginx的时候都会指向我们的主站，但是需要注意的是在这里要进行一下跳转设置，

因为在不设置的话，在百度等收录的话就会当做成两个站点，就行重复收录。

配置nginx如下：

  server_name site.com www.site.com
  
  if ($host = 'site.com') {
    rewrite ^/(.*)$ http://www.site.com/$1 permanent;
  }

