---
title: Ubuntu搭建php+apache细节
date: 2018-07-25 12:31:28
tags:
---

#Ubuntu搭建php+apache细节

Ubuntu搭建微博框架[EMicroblog](http://120.78.190.79/main.php)，其中在Linux下搭建php和apache需要注意一些事情，本文以Ubuntu为例。
<br>
<97516719@qq.com>

## 安装环境

#### Mysql Apache PHP
``` bash
$ sudo apt-get install mysql-server mysql-client
$ sudo apt-get install apache2
$ sudo apt-get install php7.0
```
以上安装过程中会有相应提示，其中mysql安装会提示设置root账户密码

#### 安装apache php module

``` bash
$ sudo apt-get install libapache2-mod-php7.0
```


#### 重启apache

```
$ sudo /etc/init.d/apache2 restart
```

通过浏览器访问 <b>`http://localhost`</b>来查看apache是否有用。
<p style="color: #2aa198">~~至此环境已经部署完毕~~</p> 有兴趣的还可以继续安装phpMyAdmin

## 权限分配

Linux内核中十分注重权限分配，对于有文件/文件夹读写需求的apache服务组`www-data`，我们需要分配文件夹权限。
```
$ cd /var/www
$ sudo chmod 777 html
```

其中`/html`为apache初始项目组。如果项目文件夹下有多层目录，将上述第二行命令递归调用
```
$ sudo chmod -R 777 html
```