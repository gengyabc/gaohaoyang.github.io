---
layout: post
title:  "5. Jenkins教程 -- jobs"
categories: 持续集成
tags:  Jenkins
author: Geng
---

* content
{:toc}

首先我们了解一下Jenkins的导航结构，然后熟悉一下Jenkins项目状态的知识，并了解远程控制Jenkins和串行运行项目的方法。

前几次我们一直在说项目（project），现在又出现一个job，他们分别是什么呢？根据[这个讨论](http://stackoverflow.com/questions/17902242/difference-between-jenkins-job-and-project)，这两个基本上是一个东西，名字基本可以互通，以下不再区分。






## 新建
进入主页，点击`新建`

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/new.png)

然后出现下图界面，`Enter an item name`中输入*DevProject1*，然后选择`构建一个自由风格的软件项目`，最后点击`OK`保存。

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/enter.png)

这样，我们在主页就可以看到这个项目了

## 配置介绍
鼠标悬浮在项目名称上，出现一个倒三角，点击出现下拉菜单，选择`配置`，进入项目配置页面。`项目名称`没什么好说的，就是项目名称而已。`描述`和上一部分**基本设置**中的内容一样。`源码管理`可以选择自己使用的管理方法，下一部分我们会讲使用git和github。其他构建方便的东西，实际操作的时候再说，现在自己熟悉下即可。

不过这里简单说下`构建触发器`的`Build periodically`中`日程表`这个部分，其实大家自己点开帮助按钮就可以得到足够的信息。
```
分钟 小时 日期 月份 星期几
```
下面相关很多部分很多取自[linux计划任务crontab使用方法](http://www.tangshuang.net/2689.html)，并做相应调整和修改。
- 分钟：表示每个小时的第n分钟，取值[0,59]，比如填写10，就是表示每个小时的第10分钟；
- 小时： 表示每天的第n个小时，取值[0,23]，比如填写3，表示每天的3点钟，如果配合上面的10，就表示每天的3点10分会执行这个任务；
- 日期： 表示每月的日期，根据不同的月进行填写，[1,31]，比如2月，就没有30,31
- 月份：取值[1,12]
- 星期几：取值[0,7]，0和7表示星期天，一般星期和日期不能同时出现，比如星期填写6，就可以表示3月的每个星期6这样的时间，如果和日期一起用，就会出现冲突，因为日期对应的星期可能不是我们填写的星期，当然，如果你确定的话，填写也没关系。

其他格式解释：

1. ,号表示一起使用，比如上面例子里，可以改成0,30 * *……，0,30的意思就是每小时的第0分钟和第30分钟会执行这个命令
2. -号表示一个区间，比如上面的改为0 3-5 * * *3-5表示3,4,5，也就是说3-5之间任何一个可选的值都会用上
3. \*号表示这个位置的任何一个有效数字都会使用，比如上面的例子里，第一列是0，其他全是，意思就是第二列[0,23]这24个数字全部用上
4. /号表示“每隔”，和逗号连接号意义不同，比如我们改为0 */2 * * * */2表示每隔两个小时执行一次，斜杠前面的值表示先限定一个区间，比如23-6/2表示在晚上11点到早上6点之间，每隔两个小时

快速帮助文档有几个例子，不明白的话可以看看，很简单。

## 熟悉Jenkins导航
我们这里先熟悉下Jenkins的导航，首先应该注意，头部有导航标识（这个应该叫啥啊，我不知道，自己取得名字，请大家能够提供学名，谢谢），如下图所示

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/nav1.png)

如果我点击一个项目名称，比如`DevProject1`

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/女.png)

进入这个项目的页面，如下，注意红框中的变化

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/nav3.png)

鼠标悬浮会有各种下拉菜单，自己可以体验。

## 项目状态
进入Jenkins主页，如果有项目的话，会如下图显示

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/pro.png)

注意红框`S`和`W`对应的标识，鼠标悬浮提示分别为“上次构建状态"和”编译晴雨表“。

`S`好理解吧，那`W`是什么呢？我们通过实验来看。

点击进入一个项目，或者通过下拉菜单，点击`立即构建`（第一次），出现下图变化

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/1.png)

`S`变蓝色，鼠标悬浮提示*success*， `W`太阳位置，悬浮提示如下图

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/sun.png)

这次模拟一次构建失败。进入项目配置，找到`构建`，因为我用的Mac，所以选择”Execute shell“，如果是win用户，请选择第一个选项。

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/2.png)

`Command`框中，输入不是mac也不是win命令的一串无意义字符，比如“asdf"，保存设置，然后点击`立即构建`（第二次），观察项目主页图标变化。

然后我再点击一次`立即构建`（第三次），继续观察项目主页图标变化。

然后删除刚才的那个无意义的命令，再次构建（第四次）。然后再次构建（第五次）。项目主页如下显示，`S`为蓝色，`W`显示乌云。鼠标悬浮，观察提示。

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/5.png)

有没有看出，`S`就是简单的本次构建成功与否，`W`是近五次构建情况总览，按照成功与失败比例，对应晴天到雷雨。

`

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/his.png)

点击某个记录，今日此构建的主页

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/bui.png)

左侧各种查看和设置选项，自己可以试试。比如点击`Console Output`，可见：

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/fal.png)

显示控制台输出，并有我们失败的提示。

## 远程启动构建
进入某个项目的设置，勾选`触发远程构建`，在`身份验证令牌`中填入一个字符串，比如“111”

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/remo.png)

根据下方提示，浏览器输入“JENKINS_URL/job/TestProject1/build?token=TOKEN_NAME ”启动构建。

“JENKINS_URL”是构建主机的地址。在这里，我们并没有远程主机连接，不过我们可以用本机实验，那么“JENKINS_URL”就是“http://0.0.0.0:8080/”。

“TOKEN_NAME”就是我们刚才设置的字符串（“111”）。

浏览器地址栏输入：“http://0.0.0.0:8080/job/TestProject1/build?token=111”，会有提示应该使用post，不要用get。不管他，点击`Proceed`继续。回到项目主页，可见已有构建。

因为使用get不安全，所以建议使用post远程构建，具体方法可以参考这里[Jenkins API Integration : Trigger Job Remotely via Jenkins API](http://www.tothenew.com/blog/jenkins-api-integration-trigger-job-remotely/)

## 项目串联运行
为了演示，快速建立三个项目如图：

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/33.png)

进入“2”的配置，找到`构建触发器`，勾选`Build after other projects are built`，`Projects to watch`填入“1”。注意下面三个单选框，这里使用默认设置。

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/22.png)

然后在`构建后操作`的操作中，选择`Build other projects`

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/bui.png)

`要构建的项目`输入“3”。

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/333.png)

保存回到“2”项目主页后，发现如下图变化

![]({{ site.url }}/assets/images/posts/CI/2017-02-04-Jenkins-jobs/chag.png)

查看其他两个项目，同样出现对应的上下级项目。
