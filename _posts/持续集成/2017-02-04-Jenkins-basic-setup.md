---
layout: post
title:  "4. Jenkins教程 -- 基本设置"
categories: 持续集成
tags:  Jenkins
author: Geng
---

* content
{:toc}

前面很多内容，已经提到了一些设置，这次介绍一些基本的设置。首先还是主页点击`系统管理`，然后进`系统设置`界面。

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-basic-setup/conf.png)




我们可以看到如下界面

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-basic-setup/all.png)
## 主目录
第一行`主目录`我们已经介绍过了，就是你的`Jenkins`设置文件存放的位置，想设置高级选项，可以点开右侧``高级...`，有疑问可以点击右侧*问号*。

## 系统消息
第二行是`系统消息`，比如我们输入”这个是系统消息“，然后点击`Apply`保存设置。

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-basic-setup/pl.png)

新开一个Jenkins页面，可以看到这个消息位于页面头部。

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-basic-setup/sys.png)

`系统消息`也可以使用HTML格式，不过我们需要进行一些设置。点击左侧`系统管理`，然后点击右侧`Configure Global Security`，找到`Markup Formatter`，将其设置为*Safe HTML*。保存设置。

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-basic-setup/html.png)

回到我们之前的系统设置页面，我们可以看到输入框下面的**Plain text**改为了**Safe HTML**，我们可以点击`预览`查看效果，如下图所示。打开一个新的主页面，查看效果。

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-basic-setup/h.png)

## Maven项目配置
配置一些Maven相关属性，其中，`执行者数量`是能够执行的job的数目。

然后详细说下`标记`，这个东西稍微复杂一些，涉及到了主从问题。其中一个用处就是用主服务器（master）的*标记*，分配从服务器（slave）的工作。以后遇到再详细说明。`用法`和`生成前等待时间`自己点开右侧的`问号`即可，这里不再重复。如果使用git，那么`SCM``签出重试次数`就是git签出如果失败了，那么最大重试次数是多少。

## 其他
其他更多设置，从名称就可以知道，不再废话
