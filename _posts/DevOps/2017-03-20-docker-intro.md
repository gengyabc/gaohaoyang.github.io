---
layout: post
title:  "1. Docker初步"
categories: DevOps
tags:  docker
author: Geng
---

* content
{:toc}


下面进入文章综（chao）述（xi）时间
## docker是什么
简单说，Docker是一个容器管理器。

[百度百科说](http://baike.baidu.com/link?url=DxBXeg9T2H0pM39VITim4Sf4oXFlY8KOerR8dR3bL5ud0LhxppJzEQUTMKtBiAjg-obmEA5g81QVznYvs15lw_#1)：Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口。

Dcoker使得用户可以将应用与其依赖包共同打包到一个标准化的单元中。





### 看图说话
先来看看docker的官方logo

![]({{ site.url }}/assets/images/posts/DevOps/2017-03-20-docker-intro/docker.png)

看到了吗？自己面壁几分钟，编一个故事出来。

这个是docker开发公司编出的故事：交付运行环境如同海运，OS如同一个货轮，每一个在OS基础上的软件都如同一个集装箱，用户可以通过标准化手段自由组装运行环境，同时集装箱的内容可以由用户自定义，也可以由专业人员制造。这样，交付一个软件，就是一系列标准化组件的集合的交付，如同乐高积木，用户只需要选择合适的积木组合，并且在最顶端署上自己的名字(最后个标准化组件是用户的app)。这也就是基于docker的PaaS产品的原型。[^1]

### 更多理论？
[百度百科](http://baike.baidu.com/link?url=DxBXeg9T2H0pM39VITim4Sf4oXFlY8KOerR8dR3bL5ud0LhxppJzEQUTMKtBiAjg-obmEA5g81QVznYvs15lw_#1)

## 安装
mac，win：到https://www.docker.com/products/docker#/，下载安装包即可。
Ubuntu：https://get.docker.com/提供了一个傻瓜是安装，直接`wget -qO- https://get.docker.com/ | sh`搞定

安装完后，运行下面命令可以看到一堆关于docker的信息
```bash
$ docker info
```

## Docker架构
![]({{ site.url }}/assets/images/posts/DevOps/2017-03-20-docker-intro/arcki.png)

Docker使用`客户端`-`服务器`架构。`客户端`（Clent）与服务器守护进程（`Daemon`）通信，此`Daemon`干各种脏活累活。`客户端`与`服务器`可以在不同机器，也可以在相同机器。


Daemon：运行于Docker服务器
客户端（Client）：Docker主要的用户界面，接受各种命令并与Daemon通信。
镜像（Image）：一个只读的模版，用于创建 Docker 容器的模板。
容器（Container）：独`立运行的一个或一组应用，镜像的实例。
寄存器（Registry）：就是镜像库。

容器与镜像的关系类似于面向对象编程中的对象与类。[^2]

![]({{ site.url }}/assets/images/posts/DevOps/2017-03-20-docker-intro/ubuntu.png)

镜像 -> 类
容器 -> 对象

`容器`都是互相隔离的。他们有唯一的`ID`和唯一的`name`。`容器`需要提供一些服务，所以`容器`允许你暴露一些特定的端口[^3]。

![]({{ site.url }}/assets/images/posts/DevOps/2017-03-20-docker-intro/image.png)

## 容器与虚拟机
具体内容自己百度，我懒得抄了。

不过有一个很大的区别就是`容器`被设计来跑一个进程，并不能很好的模仿一个完整的环境。Docker设计者强烈建议“一个容器一个进程”。

## Docker Engine 和 Docker Machine
一般当人们说 “Docker”时， 他们一般指的是"Docker Engine"。“Docker Machine”让你可以在虚拟机上安装"Docker Engine"

<br>
<br>
<br>

[^1]:http://baike.baidu.com/link?url=DxBXeg9T2H0pM39VITim4Sf4oXFlY8KOerR8dR3bL5ud0LhxppJzEQUTMKtBiAjg-obmEA5g81QVznYvs15lw_#1
[^2]:http://www.runoob.com/docker/docker-architecture.html
[^3]:https://blog.talpor.com/2015/01/docker-beginners-tutorial/
