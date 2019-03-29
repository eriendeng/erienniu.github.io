---
title: Docker部署相关
date: 2019-03-29 20:32:55
tags:
---


## Docker
Docker是由Golang开发的轻量级虚拟容器技术，主要目的是解决在生产环境中的环境、依赖配置问题和单机多部署的性能提升问题。

### 环境打包
在docker环境构建时，我们通过以下命令构建新的容器
```
docker run {imageName:Version/Tag} {command}
```

我们可以通过docker打包解决环境差别问题，下面以php-fpm环境为例：
[菜鸟教程](http://www.runoob.com/docker/docker-install-php.html)

* 选择目录并新建dockerfile
```
FROM ubuntu:16.01
MAINTAINER ERIEN 97516719@QQ.COM

ADD http://nginx.org/download/nginx-1.15.0.tar.gz
RUN tar zxf nginx-1.15.0.tar.gz
RUN mkdir /usr/local/nginx
COPY ./nginx-1.15.0 /usr/local/nginx
``` 

这样子我们就完成了在一个docker文件中配置nginx，只要在docker中启动这个文件就可以完成构建。


### 单机多部署
很容易可以想到，在同一台主机上通过docker可以简单部署多个相同的服务来模拟分布式，发挥一台主机的性能优势。在以前，会采用虚拟机的方式去进行部署。
虚拟机和docker差别如下
![性能差别](https://img-blog.csdn.net/20180711090727241?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ppbmd6aHVuYmlhbmNoZW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
很容易看出来，docker绕过了系统层面的交互，使得每个容器能够直接地和docker引擎（类似中间件）进行通信，从而避免了多次请求底层系统的内存开销。
在性能方面，docker的启动时间简直令人发指，一个基本的LNMP的docker服务的启动仅仅需要7秒左右，但如果这个效果放在虚拟机中，可能会翻好几倍。
在储存方面，docker在大部分的语言、数据库镜像的处理上，实现了简易版的体积，例如在数据库方面，数据储存的切片并不会直接储存在docker的“包”里，而是由docker层面提供一个直接和系统交互的切片空间储存，所以尽管在大型服务上线很久之后，我们查看docker的体积也仅仅占几十M左右。

（待更新）