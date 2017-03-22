---
layout: post
title:  "1. Jenkins教程 -- 持续集成初步"
categories: 持续集成
tags:  Jenkins
author: Geng
---

* content
{:toc}

## 什么是持续集成
![Continuous-Integration]({{ site.url }}/assets/images/posts/2017-02-01-continous-integration-first-step/Continuous-Integration.png "持续集成")

持续集成（Continuous integration， CI），频繁地（一天多次）将代码集成到共享的代码仓库。
Martin Fowler对持续集成是这样定义的:持续集成是一种软件开发实践，即团队开发成员频繁地集成他们的工作，通常每个成员每天至少集成一次，也就意味着每天可能`会发生多次集成。每次集成都通过自动化的构建（包括编译，发布，自动化测试)来验证，从而尽快地发现集成错误。持续集成并不能消除Bug，而是让它们非常容易发现和改正[^1]。





它的好处主要有两个[^2]：
> 1. 快速发现错误。每完成一点更新，就集成到主干，可以快速发现错误，定位错误也比较容易。
> 2. 防止分支大幅偏离主干。如果不是经常集成，主干又在不断更新，会导致以后集成的难度变大，甚至难以集成

持续集成的目的，就是让产品可以快速迭代，同时还能保持高质量。

它的核心措施是，代码集成到主干之前，必须通过自动化测试。只要有一个测试用例失败，就不能集成。

与持续集成相关的，还有两个概念，分别是持续交付和持续部署。
![]({{ site.url }}/assets/images/posts/2017-02-01-continous-integration-first-step/CI等.jpg)

### 持续交付
持续交付（Continuous delivery）指的是，频繁地将软件的新版本，交付给质量团队或者用户，以供评审。如果评审通过，代码就进入生产阶段。
持续交付可以看作持续集成的下一步。它强调的是，不管怎么更新，软件是随时随地可以交付的。

### 持续部署
持续部署（continuous deployment）是持续交付的下一步，指的是代码通过评审以后，自动部署到生产环境。
持续部署的目标是，代码在任何时刻都是可部署的，可以进入生产阶段。
持续部署的前提是能自动化完成测试、构建、部署等步骤。

### 三者关系
![]({{ site.url }}/assets/images/posts/2017-02-01-continous-integration-first-step/relation.png)

## 本系列目的
在借鉴[从0到1搭建移动App功能自动化测试平台](http://debugtalk.com/post/build-app-automated-test-platform-from-0-to-1-backgroud-introduction/)的基础上，搭建适合自己使用的App及网站测试平台。 
本系列还将使用Appium，rails等服务。总体系统系统构建思路将在未来补上。

![]({{ site.url }}/assets/images/posts/2017-02-01-continous-integration-first-step/集成.png)

## 使用Jenkins做持续集成
持续集成的工具有很多，想要了解具体情况可以参考[Comparison of continuous integration software](https://en.wikipedia.org/wiki/Comparison_of_continuous_integration_software)，这里不做更过介绍，而是直接使用Jenkins。

Jenkins是一个开源软件项目，旨在提供一个开放易用的软件平台，使软件的持续集成变成可能。Jenkins是基于Java开发的一种持续集成工具，用于监控持续重复的工作，功能包括：
1. 持续的软件版本发布/测试项目。
2. 监控外部调用执行的工作。

### Jenkins在持续集成中的位置

![]({{ site.url }}/assets/images/posts/2017-02-01-continous-integration-first-step/introduction-to-jenkins-7-638.jpg)

### 安装及初始化
1. 下载jenkins war文件：https://jenkins.io/

   ![]({{ site.url }}/assets/images/posts/2017-02-01-continous-integration-first-step/jenkins.png)
2. 运行：java -jar jenkins.war
3. 登录：http://0.0.0.0:8080/
4. 根据提示进行设置。具体为：根据提示，找到生成的随机密码，复制下来，粘贴到密码框，点击确定。c1ea9eccb8ea49ef9f4bdab2e236f41b
5. 安装或者不安装推荐的插件，都可以。
6. 如果提示要注册用户，注册即可。我没有选择任何插件，进入了`admin`用户
7. 完

![]({{ site.url }}/assets/images/posts/2017-02-01-continous-integration-first-step/main.png)

### 修改管理员账户密码

页面右上角点击用户名，下拉菜单，选择`设置`

![]({{ site.url }}/assets/images/posts/2017-02-01-continous-integration-first-step/step2.png)

修改密码，方便以后管理和维护

![]({{ site.url }}/assets/images/posts/2017-02-01-continous-integration-first-step/4.png)

### 配置文件位置
Mac：~/.jenkins
也可以通过：`系统管理` -> `系统设置` 查看
![]({{ site.url }}/assets/images/posts/2017-02-01-continous-integration-first-step/change.png)

### 修改配置文件位置？
不管为什么，我想要修改配置文件位置怎么办？
1. 将原始位置的所有文件复制到新的位置
2. 修改环境变量
   * mac

     打开~/.bash_profile，添加：
     ```bash
     # 根据自己要求修改
     export JENKINS_HOME=/path/to/jenkins_home/
     ```
     然后terminal中
     ```bash
     $ source ~/.bash_profile
     ```

   * win

![]({{ site.url }}/assets/images/posts/2017-02-01-continous-integration-first-step/win.png)

[^1]:http://baike.baidu.com/view/5253255.htm
[^2]:http://www.ruanyifeng.com/blog/2015/09/continuous-integration.html
