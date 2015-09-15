title: "更新升级PHP版本"
date: 2015-06-26 22:44:42
tags: [PHP]
category: [PHP]
---

现版本：5.5.21

更新版本： 5.6.10


### 查看原来安装的配置参数

执行`php -v | grep config`

### 安装

根据之前的安装配置执行:

```bash
$ ./configure --prefix=/usr/local/php5/ --with-libxml-dir --enable-cli --enable-fpm --with-mysqli --with-gettext --enable-mbstring --with-mcrypt --with-openssl --disable-debug --enable-opcache --with-pdo-mysql --with-mysql
$ make && make install
```

### 剩余工作

安装成功后, 并不意味着升级工作完成了。当我们运行 `php -v` 时会有错误抛出, 那是因为两个版本的扩展编译标示前后不一致，所以我们接下的来的工作就是重新编译扩展。

在 `path/to/php5/lib/php/extensions` 目录下有两个文件夹: `no-debug-non-zts-20121212/` (之前版本扩展安装目录) 和 `no-debug-non-zts-20131226/` (新版本扩展安装目录), 由于新升级的PHP的标示是 `20131226` , 所以对应的扩展编译也要对应上。

例如, 我之前的扩展源文件目录 `/usr/local/src`, 我们并不是直接在之前安装的扩展目录中直接配置编译安装就可以了, 我们需要删除原来解压出来的文件, 重新解压扩展源文件, 进行扩展安装:

```bash
//例如重新编译安装 Imagick
$ rm -rf imagick-3.1.1
$ tar -zxvf imagick-3.1.1.tar
$ cd imgick-3.1.1
$ path/to/php5/bin/phpize
& ./configure --with-php-config=/path/to/php5/bin/php-config
$ make && make install
```

接下来最后就是修改 `php.ini` 中的 `extension_dir` 为新的目录.

> 将php加入环境变量: `export PATH=$PATH:/usr/local/php5/bin`

完.
