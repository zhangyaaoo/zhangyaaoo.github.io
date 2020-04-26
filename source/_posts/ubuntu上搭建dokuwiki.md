---
title: ubuntu上搭建dokuwiki
date: 2020-01-13 20:00:38
tags:
 - dokuwiki
 - ubuntu
categories:
 - Tools
---

[dokuwiki 官方使用手册](https://www.dokuwiki.org/manual)

> 搭建环境：
>
> ubuntu（16.04）


### 安装web服务器和PHP

```bash
sudo apt-get install apache2 php7.0 php7.0-fpm php7.0-cli php-apcu php7.0-gd php7.0-xml php7.0-curl php7.0-json php7.0-mcrypt php7.0-cgi libapache2-mod-php7.0 php-patchwork-utf8
```

### 下载dokuwiki

[下载链接](https://www.dokuwiki.org/dokuwiki)

解压得到的文件夹 dokuwiki 放到 /var/www/html/ 下，如：

```bash
zyao@ubuntu:~$ ls /var/www/html/
dokuwiki  index.html
zyao@ubuntu:~$ ls /var/www/html/dokuwiki/
bin  conf  COPYING  data  doku.php  feed.php  inc  index.php  lib  README  vendor  VERSION
zyao@ubuntu:~$
```

更改文件夹权限，如：

```bash
cd /var/www/html/dokuwiki
sudo chown -R www-data:www-data data conf
```

### 安装
浏览器输入网址：`http://你的服务器IP/dokuwiki/install.php`


### 设置权限

```bash
# 1、
sudo mv data /home/zyao/.dokuwiki/ -f
# 2、追加一行内容：$conf['savedir'] = '/home/yourname/data/'; 到conf/local.php
# 3、
mv conf /home/zyao/.dokuwiki/ -f
cd inc/
vi preload.php
mv bin /home/zyao/.dokuwiki/ -f
```


#### 将目录移出根文档

为了避免任何对上述目录的访问，最安全的方法就是把它们从 Web 服务所谓的“根文档”中移出来。

##### data 目录

- 从根文档中移出 "data" 目录（和它所有的内容）
- 编辑 [[config:savedir]] 设置，使它指向 "data" 目录的新位置。

例如，如果把 "data" 目录移动到 "/home/yourname/data"，添加以下这一行到 "conf/local.php":

```bash
  $conf['savedir'] = '/home/yourname/data/';
```

##### conf 目录

- 从根文档中移出 "conf" 目录（和它所有的内容）
- 在 "inc" 目录内创建一个名为 preload.php 的文件,设置 "DOKU_CONF" 定义并使它指向 "conf" 目录的新地址。

例如，如果把 "conf" 目录移动到 "/home/yourname/conf",创建以下的 "inc/preload.php"文件：

<code php inc/preload.php>
```php
<?php

define('DOKU_CONF','/home/yourname/conf/');
</code>
```
##### bin 目录

bin 目录包含了命令行工具。如果你没有 shell 可以访问你的服务器，你可以简单的删除该目录和它的内容。否则只要把它移出文档根即可。不需要更多的配置工作。

##### inc 目录

从文档根中移出该目录，直到目前还没有简单的方法。但是因为它不包含任何的敏感数据，所以不值得努力尝试移出它了。


[参考这里](https://www.dokuwiki.org/start?id=zh:security)



参考博客：

[参考一](https://blog.csdn.net/wszll_Alex/article/details/80250249)、[参考二](https://linux.cn/article-8178-1.html)、[参考三](https://my.oschina.net/xiangxw/blog/36233)、[dokuwiki升级](https://blog.csdn.net/boyStray/article/details/77113945)



**效果展示：**
![](https://gitee.com/zhangyaaoo/blogpics/raw/master/misc/2019-10-15_152955-dokuwiki-mainpage.png)

