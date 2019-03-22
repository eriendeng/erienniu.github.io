---
title: 高版本macOS中安装软件权限注意事项
date: 2018-07-25 14:52:00
tags:
---

#奇妙旅程

-----

###mysql莫名其妙写入失败！
删除后brew不能使用需要brew update<br>
update需要 chmod /usr/local<br>
高版本的根本不允许好吗！<br>
sudo都能 operation not permitted！<br>

-----

###原因

[MAC /usr/bin/目录下 Operation not permitted的解决](https://blog.csdn.net/yemao_guyue/article/details/80575532)<br>
真凶的解决办法和原理<br>
但是破坏原有机制不是好办法

-----

###解决方法
跳过这个步骤 卸载brew后重装<br>
[chown: /usr/local: Operation not permitted问题解决](https://blog.csdn.net/yemao_guyue/article/details/80575532)<br>
纯属记录

-----

###新状况
虚拟环境识别不出_mysql包<br>
原因大概是mysql8有点新不支持 找不到解析包`libmysqlclient.20.dylib`<br>
其实 `/usr/local/Cellar/mysql/8.0.11/lib` 下有一个文件叫 `libmysqlclient.21.dylib`<br>
目前找不到别的方法，名字强行改一下竟然 好了。。。。。<br>
终结