---
title: 新centOS主机配置mysql和nginx
date: 2018-07-25 14:54:04
tags:
---

#  新centOS主机配置mysql和nginx

-----

## *mysql*

```
//获取版本安装repo 这里安装mysql57
[user]# wget http://repo.mysql.com/mysql57-community-release-el7-8.noarch.rpm
[user]# rpm -ivh mysql57-community-release-el7-8.noarch.rpm
//安装后续服务端 用yum神器
[user]# yum install mysql-server
//一路 yes到complete为止

//启动 mysql获取密码
[user]# service mysqld start
[user]# grep "password" /var/log/mysqld.log

//改密码开放远程权限
[user]# mysql -u root -p
mysql> alter user user() indentified by 'NEW PASSWORD';
mysql> update mysql.user host = '%' where user = 'root';

```

-----

## *Nginx*

```

//要求sudo安装 yes到底
[user]# sudo yum install nginx

//启动
[user]# sudo systemctl nginx

```