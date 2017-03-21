---
layout: post
title:  "2 Docker教程"
categories: DevOps
tags:  docker
author: Geng
---

* content
{:toc}


翻译自https://prakhar.me/docker-curriculum/，并有少许改动和修正。

## 试玩[Busybox](https://en.wikipedia.org/wiki/BusyBox)
Busybox可以认为就是一个简化的linux系统






运行
```bash
$ docker pull busybox
```
> 由于系统权限问题，上述`命令可能会有问题。Mac系统请查看Docker引擎正在运行。Linux请尝试“sudo docker pull busybox”

*pull*命令将`busybox`镜像从Decker寄存器提取并保存在你的系统。

运行下面命令，可以看到系统上安装的所有镜像
```
$ docker images
```
### run
让我们运行一下这个镜像
```
$ docker run busybox
```
嗯。。。有什么变化吗？其实后台发生了很多事情，虽然你现在看不出来。当你运行*run*命令时，`客户端`找到镜像（这里就是busybox），载入容器然后在这个容器中运行命令。我们刚才并没有提供任何命令，所以这个容器启动，空跑一通就直接退出了。

那我们试着提供一个命令
```bash
$ docker run busybox echo "hello from busybox"
hello from busybox
```

赞！终于看到些变化了。这一个命令，我们经历了启动一个镜像，运行一个命令，然后退出。而且这个过程真的很快！你用是任何一个虚拟机能做到这个速度吗？知道Docker很快了吧。

我们再来看看*docker ps*命令，它显示所有正在运行的容器。

```bash
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

因为现在并没有运行的容器，我们看不到什么信息。我们是一个更有用的形式：*docker ps -a*

```bash
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
305297d7a235        busybox             "uptime"            11 minutes ago      Exited (0) 11 minutes ago                       distracted_goldstine
ff0a5c3750b9        busybox             "sh"                12 minutes ago      Exited (0) 12 minutes ago                       elated_ramanujan
So what we see above is a list of all containers that we ran. Do notice that the STATUS column shows that these containers exited a few minutes ago.
```
看到了吗？这是我们之前运行过的容器的清单。其中`STATUS`显示这些容器在多久前退出。

那我们可以运行多条命令吗？我们试下启动镜像的一个`bash`可以吗？

```bash
$ docker run -it busybox sh
/ # ls
bin   dev   etc   home  proc  root  sys   tmp   usr   var
/ # uptime
 05:45:21 up  5:58,  0 users,  load average: 0.00, 0.01, 0.04
```
*it*将我们连接到容器中一个交互的tty。其中, *-t*选项让Docker分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上， *-i*则让容器的标准输入保持打开，即交互状态。

这里，我们可以输入任何我们想输入的命令。

> 在容器中，尝试新建一个文件，比如*touch myfile.txt*,然后*ls*确认新建成功。输入*exit*，点击*Enter*。再次*docker run -it busybox sh*进入容器，运行*ls*。有没有发现，刚才新建的文件消失了。这是因为我们使用*run*启动镜像，每次都会创建全新的容器。

下面在说下怎么删除容器。我们刚才看到，即使我们退出容易，仍然可以通过*docker ps -a*命令看到退出容器的残留。随着容器建立退出次数越来越多，这些残留将会占用大量硬盘空间。因此，如果没有特殊需求，最好用完之后清除这些残留。我们可以使用*docker rm*命令，后面填容器的container ID（通过*docker ps -a*可见此ID）

```bash
$ docker rm 305297d7a235 ff0a5c3750b9
305297d7a235
ff0a5c3750b9
```

如果想更快地删除大量容器，可以：

```bash
$ docker rm $(docker ps -a -q -f status=exited)
```

最后，还可以通过*docker rmi*删除不用的镜像

### 容器其他命令
*docker start | restart | stop*：启动停止的容器，重启容器，停止容器
*docker exec*：运行正在运行的一个容器的命令

建议自己查查，因为后面不用这些命令，这里略过，以免影响心情。

## Docker的Webapp
玩够了，我们现在可以搞点真实的东西了。

### 静态网站
我们一步步来。我们首先尝试建立一个巨简单的静态网站。我们会从`Docker Hub`拖下来一个镜像，跑一个容器，感受一下跑一个网站服务是多么简单。

开始干活。这个静态网站托管在在Docker寄存器 - prakhar1989/static-site。下载这个镜像然后跑起来，以下这一条命令搞定：

```bash
$ docker run prakhar1989/static-site
```

因为本地没有这个镜像，所以首先下载才能跑起来。等到看到`Nginx is running...`就说明已经跑起来了。那怎么看到网站呢？我们怎么才能网站这个容器？

额。。。现在客户端没有暴露任何端口，我们需要重新运行*docker run*以开放端口。同时，当关掉终端时，我们也不想关掉运行中的容器。满足这种需求的模式叫做分离模式（detached mode）。

```bash
$ docker run -d -P --name static-site prakhar1989/static-site
e61d12292d69556eabe2a44c16cbd54486b2527e2ce4f95438e504afb7b02810
```

上述命令中，, *-d*进入分离模式, *-P*将所有端口暴露到随机端口。*--name*后面是我们起的名字。现在我们通过下面命令查看端口吧

```bash
$ docker port static-site
80/tcp -> 0.0.0.0:32769
443/tcp -> 0.0.0.0:32768
```

浏览器输入“http://localhost:32769”访问。当然，我们也可以指定端口，比如

```bash
$ docker run -p 8888:80 prakhar1989/static-site
Nginx is running...
```

![]({{ site.url }}/assets/images/posts/DevOps/2017-03-21-docker-tutorial/web.png)

想要停止容器，使用*docker stop 容器id* 命令。

是不是巨简单？你已经知道怎么使用Docker镜像跑起来一个网站了，那么，我们怎么建立自己的镜像呢？

### Docker镜像
本小结介绍如何建立自己的镜像。

简单来讲，Docker镜像就像是一个git仓库，镜像可以做出有很多改变和版本的提交。如果不指定版本，默认使用最新版。

比如像要拉取某个版本的Ubuntu

```bash
$ docker pull ubuntu:12.04
```


想要得到一个Docker镜像，或者从一个Docker寄存器（比如[Docker Hub](https://hub.docker.com/)）拉取，或者自己建立。你可以在Docker Hub搜索自己想要的镜像。

镜像有一个需要特变注意的区别是基镜像和子镜像（base and child images）。

基镜像没有任何父镜像，一般是系统镜像比如Ubuntu和刚才用到的busybox。子镜像基于基镜像并于其上添加一些功能。这样就有了官方镜像和用户镜像。

官方镜像由Docker员工维护，用户镜像由类似你我的用户建立。

### 我们的第一个镜像
需要python支持，没有的话自己去安装。

我们的目标是建立一个[Flash](http://flask.pocoo.org/)应用。这个应用是原作者做的，每次载入会显示一个随机的小猫动图。请[复制此库到本地](https://github.com/prakhar1989/docker-curriculum/)。

#### 验证本地工作
进入`flask-app`路径然后运行此应用，如下：

```bash
$ cd flask-app
$ pip install -r requirements.txt
$ python app.py
 * Running on http://0.0.0.0:5000/ (Press CTRL`C to quit)
 ```
 
一切顺利的话，你可以在`http://localhost:5000`看到效果。

> 如果出现权限问题，可能需要sudo，或者你不想吧各种包安装到系统层面，可以试试*pip install --user -r requirements.txt*

不错吧？下一步我们就要用此镜像建立一个自己的镜像。

#### 建立自己的镜像
如前所述，所有的用户镜像都是基于基镜像。

因为我们这个应用使用Python写成，我们使用的基镜像是[Python 3](https://hub.docker.com/_/python/)。下载：

```bash
$ docker pull python:3-onbuild
```

`onbuild`版的镜像包括可以使应用启动的一些帮助项。现在我们有了足够的内容：可工作的app和基镜像。下一步做什么呢？我们使用**Dockerfile**

## Dockerfile
### 什么是Dockerfile
**Dockerfile**就是一个简单的文本文件，它里面包含了创建镜像的时候Docker客户端调用的命令。这是一个简化自动化镜像创建的过程。你在**Dockerfile**写的命令和Linux命令几乎相同，是不是很爽？不用学就会了吧？

下面我们脑补一下**Dockerfile**应该有什么内容。
1. 基镜像是谁？
2. 怎么把我们需要的文件等装到基镜像？
3. 怎么访问我们的应用？使用哪个端口？
4. 怎样启动应用？

我们看下**Dockerfile**的内容（位于`flask-app`文件夹）

```bash
# 基镜像
FROM python:3-onbuild

# 端口
EXPOSE 5000

# 启动应用
CMD ["python", "./app.py"]
```

我们可以看到，这个文件没有回答第二个问题，如何是好？
不用担心，因为我们的**Dockerfile**位于`flask-app`文件夹，且使用`onbuild`版本，第二个问题自动解决了。

### 使用Dockerfile
怎么用呢？让*docker build*命令去干就行了。

首先，没有注册`Docker hub`的同学要去注册，记录自己的*用户名*。
*cd*到**Dockerfile**路径，然后：

```bash
$ docker build -t 你的用户名/catnip .
Sending build context to Docker daemon 8.704 kB
Step 1 : FROM python:3-onbuild
# Executing 3 build triggers...
Step 1 : COPY requirements.txt /usr/src/app/
 ---> Using cache
Step 1 : RUN pip install --no-cache-dir -r requirements.txt
 ---> Using cache
Step 1 : COPY . /usr/src/app
 ---> 1d61f639ef9e
Removing intermediate container 4de6ddf5528c
Step 2 : EXPOSE 5000
 ---> Running in 12cfcf6d67ee
 ---> f423c2f179d1
Removing intermediate container 12cfcf6d67ee
Step 3 : CMD python ./app.py
 ---> Running in f01401a5ace9
 ---> 13e87ed1fbc2
Removing intermediate container f01401a5ace9
Successfully built 13e87ed1fbc2
```

其中*-t*标记来添加*tag*，指定新的镜像的用户信息。 “.” 是 **Dockerfile**所在的路径（当前目录），也可以替换为一个具体的 Dockerfile的路径。

### 跑起来
```bash
$ docker run -p 8888:5000 用户名/catnip
* Running on http://0.0.0.0:5000/ (Press CTRL`C to quit)
```

访问“http://0.0.0.0:8888/”看看效果吧。

祝贺你，成功了！

### Docker on AWS
算了吧

## 多容器环境
上面部分只运行了一个Docker。但是为了运行一个应用，你还用很多其他依赖吧，数据库得有吧？

这部分我们就讨论下怎么运行有依赖的应用。具体来讲，就是讨论运行和管理多容器环境。

将各种服务解耦很重要，使用容器隔离各个服务。这样就可以针对各个服务进行单独优化。

## SF Food Trucks为例

![]({{ site.url }}/assets/images/posts/DevOps/2017-03-21-docker-tutorial/shot.png)

[此应用](https://github.com/prakhar1989/FoodTrucks)包含*Flask*后台和[Elasticsearch](http://learn.elastic.co/)服务。自然我们想把它分成两个容器。如果以后某部分成为性能瓶颈，我们可以通过增加容器单独优化。

### Elasticsearch容器
建立容器应该不难吧，我们已经建立过一个*Flask*容器，那我们看看有没有*Elasticsearch*容器：

```bash
$ docker search elasticsearch
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
elasticsearch                     Elasticsearch is a powerful open source se...   697       [OK]
itzg/elasticsearch                Provides an easily configurable Elasticsea...   17                   [OK]
tutum/elasticsearch               Elasticsearch image - listens in port 9200.     15                   [OK]
barnybug/elasticsearch            Latest Elasticsearch 1.7.2 and previous re...   15                   [OK]
digitalwonderland/elasticsearch   Latest Elasticsearch with Marvel & Kibana       12                   [OK]
monsantoco/elasticsearch          ElasticSearch Docker image                      9                    [OK]
```

我们可以看到有一个官方版本（没有前缀那个）。我们迅速就可以把它运行起来：

```bash
$ docker run -dp 9200:9200 --name es elasticsearch
d582e031a005f41eea704cdc6b21e62e7a8a42021297ce7ce123b945ae3d3763

$ curl 0.0.0.0:9200
{
  "name" : "Ultra-Marine",
  "cluster_name" : "elasticsearch",
  "version" : {
    "number" : "2.1.1",
    "build_hash" : "40e2c53a6b6c2972b3d13846e450e66f4375bd71",
    "build_timestamp" : "2015-12-15T13:05:55Z",
    "build_snapshot" : false,
    "lucene_version" : "5.3.1"
  },
  "tagline" : "You Know, for Search"
}
```


### Flask容器
下一步就是跑起来`Flask``容器`。

首先我们需要`Dockerfile`。这次，除了Python，我们需要JavaScript依赖和nodejs。因为有了自定义构建的步骤，我们需要从Ubuntu基镜像开始。

`Dockerfile`如下：

```bash
# start from base
FROM ubuntu:14.04
MAINTAINER Prakhar Srivastav <prakhar@prakhar.me>

# install system-wide deps for python and node
RUN apt-get -yqq update
RUN apt-get -yqq install python-pip python-dev
RUN apt-get -yqq install nodejs npm
RUN ln -s /usr/bin/nodejs /usr/bin/node

# copy our application code
ADD flask-app /opt/flask-app
WORKDIR /opt/flask-app

# fetch app specific deps
RUN npm install
RUN npm run build
RUN pip install -r requirements.txt

# expose port
EXPOSE 5000

# start app
CMD [ "python", "./app.py" ]
```

我们从基镜像开始，安装依赖项。*yqq*说明任何询问都答*yes*，而且创建了到node的符号链接来保证兼容性。
		
然后使用*ADD*命令将我们的应用复制到容器中的`/opt/flask-app`。然后我们将这个目录设为工作目录，这样下面的操作将会在这个目录进行。

现在系统级别的依赖都搞定了，下面就要安装应用级的依赖了。首先使用*npm*安装然后运行*pip*。后面步骤前别按介绍过，不再详述。

最后，跑起来吧（其实是爬，因为要下载很多东西）：

```bash
$ docker build -t 你的用户名/foodtrucks-web .
```

都安装好后，再次运行会很快。我们试试：

```bash
$ docker run -P prakhar1989/foodtrucks-web
Unable to connect to ES. Retying in 5 secs...
Unable to connect to ES. Retying in 5 secs...
Unable to connect to ES. Retying in 5 secs...
Out of retries. Bailing out...
```

额。。。我们的应用跑步起来，因为它不知道`Elasticsearch`在哪。怎么办呢？

### Docker Network
首先，我们想想怎么解决这个问题。想想我们学过什么命令可以用来给我们一些启示吗？*docker ps*如何？其实除了这个命令我们不知道该干什么了吧。

我们来试下：
```bash
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                              NAMES
e931ab24dedc        elasticsearch       "/docker-entrypoint.s"   2 seconds ago       Up 2 seconds        0.0.0.0:9200->9200/tcp, 9300/tcp   cocky_spence
```

这段输出跟我们说在`0.0.0.0:9200`有一个`ES`容器可以访问。我们能告诉`Flask`去访问这里吗？

那么我们就得和`Flask`容器中的应用说去访问主机的`0.0.0.0:9200`吧。但是`ES`容器也运行在`0.0.0.0`，只不过是另外一个端口。那么并不能通过访问这个`IP`来交流。哪还有什么`IP`可用吗？

下面我们分析一下docker的网络。当docker安装之后，自动建立三个网络：

```bash
$ docker network ls
NETWORK ID          NAME                DRIVER
075b9f628ccc        none                null
be0f7178486c        host                host
8022115322ec        bridge              bridge
```

`bridge`网络是运行的默认网络。我们查看下：

```bash
$ docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "8022115322ec80613421b0282e7ee158ec41e16f565a3e86fa53496105deb2d7",
        "Scope": "local",
        "Driver": "bridge",
        "IPAM": {
            "Driver": "default",
            "Config": [
                {
                    "Subnet": "172.17.0.0/16"
                }
            ]
        },
        "Containers": {
            "e931ab24dedc1640cddf6286d08f115a83897c88223058305460d7bd793c1947": {
                "EndpointID": "66965e83bf7171daeb8652b39590b1f8c23d066ded16522daeb0128c9c25c189",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        }
    }
]
```

可以见到我们的容器`e931ab24dedc`在"Containers"中列出，而且分配了一个IP：172.17.0.2。这个IP是我们想要用的那个吗？我们运行`flask`容器试试。

```bash
$ docker run -it --rm 你的用户名/foodtrucks-web bash
root@35180ccc206a:/opt/flask-app# curl 172.17.0.2:9200
bash: curl: command not found
root@35180ccc206a:/opt/flask-app# apt-get -yqq install curl
root@35180ccc206a:/opt/flask-app# curl 172.17.0.2:9200
{
  "name" : "Jane Foster",
  "cluster_name" : "elasticsearch",
  "version" : {
    "number" : "2.1.1",
    "build_hash" : "40e2c53a6b6c2972b3d13846e450e66f4375bd71",
    "build_timestamp" : "2015-12-15T13:05:55Z",
    "build_snapshot" : false,
    "lucene_version" : "5.3.1"
  },
  "tagline" : "You Know, for Search"
}
root@35180ccc206a:/opt/flask-app# exit
```

*--rm*说明运行完就自动删除这个容器。我们进入容器后，运行了一个*curl*命令，但是提示此命令没有安装，所以需要安装之后，再次运行。然后，大功告成！

虽然我们知道了怎么样让它们之间通信，但是我们仍然有两个问题需要解决：

1. `bridge`网络是所有容器共享的，不够安全。
2. 告诉`Flask`连接我们让他连接的地址。

Docker允许我们建立我们自己的网络，还能是它们互相隔离.

首先，我们建立一个自己的网络

```bash
$ docker network create foodtrucks
1a3386375797001999732cb4c4e97b88172d983b08cd0addfcb161eed0c18d89

$ docker network ls
NETWORK ID          NAME                DRIVER
1a3386375797        foodtrucks          bridge
8022115322ec        bridge              bridge
075b9f628ccc        none                null
be0f7178486c        host                host
```

这个命令新建了一个名为`foodtrucks`的`birdge`网络。Docker网路的详细内容，请阅读[官方文档](https://docs.docker.com/engine/userguide/networking/)。

现在我们有了网络，我们可以使用 *--net*将我们的容器运行在这个网络。首先，我们先把之前的容器停止。

```bash
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                              NAMES
e931ab24dedc        elasticsearch       "/docker-entrypoint.s"   4 hours ago         Up 4 hours          0.0.0.0:9200->9200/tcp, 9300/tcp   cocky_spence

$ docker stop e931ab24dedc
e931ab24dedc

$ docker run -dp 9200:9200 --net foodtrucks --name es elasticsearch
2c0b96f9b8030f038e40abea44c2d17b0a8edda1354a08166c33e6d351d0c651

$ docker network inspect foodtrucks
[
    {
        "Name": "foodtrucks",
        "Id": "1a3386375797001999732cb4c4e97b88172d983b08cd0addfcb161eed0c18d89",
        "Scope": "local",
        "Driver": "bridge",
        "IPAM": {
            "Driver": "default",
            "Config": [
                {}
            ]
        },
        "Containers": {
            "2c0b96f9b8030f038e40abea44c2d17b0a8edda1354a08166c33e6d351d0c651": {
                "EndpointID": "15eabc7989ef78952fb577d0013243dae5199e8f5c55f1661606077d5b78e72a",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {}
    }
]
```


我们这次给了这个容器一个名字*es*，运行在我们自定义的网络，其他都和以前一样。我们在试着将我们的`flask`容器运行在这个网络。

```bash
$ docker run -it --rm --net foodtrucks 你的账户名/foodtrucks-web bash
root@53af252b771a:/opt/flask-app# cat /etc/hosts
172.18.0.3  53af252b771a
127.0.0.1   localhost
::1 localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.18.0.2  es
172.18.0.2  es.foodtrucks

root@53af252b771a:/opt/flask-app# curl es:9200
bash: curl: command not found
root@53af252b771a:/opt/flask-app# apt-get -yqq install curl
root@53af252b771a:/opt/flask-app# curl es:9200
{
  "name" : "Doctor Leery",
  "cluster_name" : "elasticsearch",
  "version" : {
    "number" : "2.1.1",
    "build_hash" : "40e2c53a6b6c2972b3d13846e450e66f4375bd71",
    "build_timestamp" : "2015-12-15T13:05:55Z",
    "build_snapshot" : false,
    "lucene_version" : "5.3.1"
  },
  "tagline" : "You Know, for Search"
}
root@53af252b771a:/opt/flask-app# ls
app.py  node_modules  package.json  requirements.txt  static  templates  webpack.config.js
root@53af252b771a:/opt/flask-app# python app.py
Index not found...
Loading data in elasticsearch ...
Total trucks loaded:  733
 * Running on http://0.0.0.0:5000/ (Press CTRL`C to quit)
root@53af252b771a:/opt/flask-app# exit
```

搞定！下一步我们将容器正式运行起来。

```bash
$ docker run -d --net foodtrucks -p 5000:5000 --name foodtrucks-web 你的用户名/foodtrucks-web
2a1b77e066e646686f669bab4759ec1611db359362a031667cacbe45c3ddb413

$ docker ps
CONTAINER ID        IMAGE                        COMMAND                  CREATED             STATUS              PORTS                              NAMES
2a1b77e066e6        prakhar1989/foodtrucks-web   "python ./app.py"        2 seconds ago       Up 1 seconds        0.0.0.0:5000->5000/tcp             foodtrucks-web
2c0b96f9b803        elasticsearch                "/docker-entrypoint.s"   21 minutes ago      Up 21 minutes       0.0.0.0:9200->9200/tcp, 9300/tcp   es

$ curl -I 0.0.0.0:5000
HTTP/1.0 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 3697
Server: Werkzeug/0.11.2 Python/2.7.6
Date: Sun, 10 Jan 2016 23:58:53 GMT
```

进入*http://0.0.0.0:5000*享受一个你的应用吧。这个过程虽然看起来很恐怖，实际只有四条命令。原作者将其写入一个[bash script](https://github.com/prakhar1989/FoodTrucks/blob/master/setup-docker.sh)：

```bash
#!/bin/bash

# build the flask container
docker build -t prakhar1989/foodtrucks-web .

# create the network
docker network create foodtrucks

# start the ES container
docker run -d --net foodtrucks -p 9200:9200 -p 9300:9300 --name es elasticsearch

# start the flask app container
docker run -d --net foodtrucks -p 5000:5000 --name foodtrucks-web prakhar1989/foodtrucks-web
```

下次想运行这个应用，只要这样既可：

```bash
$ git clone https://github.com/prakhar1989/FoodTrucks
$ cd FoodTrucks
$ ./setup-docker.sh
```

完！很清爽吧！

还有更多docker内容，可以访问[原文教程](https://prakhar.me/docker-curriculum)。

本人也正在学习，以后可能会有更新
