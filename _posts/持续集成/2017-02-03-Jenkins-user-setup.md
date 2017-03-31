---
layout: post
title:  "3. Jenkins教程 -- 用户设置"
categories: 持续集成
tags:  Jenkins
author: Geng
---

* content
{:toc}

我们已经初步了解了什么是持续集成，并知道了Jenkins的一些基本信息。可能有读者已经发现，我们现在只有一个管理员账号，而且即使新建账号，也是管理员权限。显然，这对安全性来说不是一个好消息。幸运的是，Jenkins带给我们一种角色管理机制，可以使我们方便的控制用户的权限。





## 建立用户
主页点击`系统管理` -> `管理用户`，可见现在只有一个管理员用户

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/user.png)

点击右侧`新建用户`

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/new.png)

输入所需信息

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/create.png)

现在，我们有两个用户

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/users.png)

退出*admin*账号，登录*user1*账号，确定没有问题，再次退出，返回*admin*账号

## 分配用户角色
新建的用户有和管理员一样的权限，不安全，我们需要限制其权限。
我们使用*角色*来管理权限。简单来说就是*管理员*创建若干角色，比如*开发者*，*测试者*等，每个角色都有对应的一些权限。我们还有若干用户，比如*用户1*，*用户2*等，其中*用户1*做开发，*用户2*做测试。那么如果我们设定*用户1*的角色是*开发者*，*用户2*的角色是*测试者*，就可以很快给各个用户分开好权限。下次再有更多用户，我们只要给他们设定相应的角色即可，而不用对每个用户单独设定，这样便可以简化我们的工作。

### 安装所需插件

进入`系统管理` -> `插件管理`，点击`可选插件`，然后在`过滤`中输入*role*
选中出现的插件，然后点击`直接安装`
![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/role.png)

然后选择`安装完成后重启``Jenkins`即可

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/restart.png)

### 设置安全策略

点击`系统管理`-> `Configure Global Security`, 选中图示的`Role-Based Strategy`，然后保存设置。

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/str.png)

退出*admin*账号，登录*user1*账号，发现出现下图错误，说明配置生效。这个错误说明未经*admin*授权角色，其他用户没有任何权限。

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/deny.png)

### 创建角色
为了给其他用户赋予合适的工作权限，我们需要首先创建角色。
重新登录*admin*

点击`系统管理`-> `Configure Global Security`-> `Manage and Assign Roles`,进入如下界面。

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/roles.png)

进入`Manage Roles`，我们看到三中角色分类，*Global roles（全局角色）*，*Project roles（项目角色）*，*Slave roles（奴隶角色）*。项目角色与全局角色的区别就是，项目角色只能管理项目，没有管理jenkins的权限配置。

- Global roles（全局角色）：管理员等高级用户可以创建基于全局的角色
- Project roles（项目角色）： 针对某个或者某些项目的角色
- Slave roles（从角色）：节点相关的权限

我们可以发现，*admin角色*拥有所有权限。

#### 添加一个全局角色
下面输入框中，添加一个全局角色，比如叫做*employee*

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/emp.png)

然后为其设置`Overrall`（全局的）`Read`和`View`的所有权限，如下图

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/read.png)

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/view.png)

#### 添加一个项目角色
`Role to add`中，添加"developer"，`Pattern`中，添加"Dev.\*"

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/dev.png)

分配给此类项目job所有权限

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/pro.png)

类似方法，可以添加一个名为"tester"的*项目角色*，`Pattern`设置为"Test.*"，并且赋予所有权限。

`Pattern`这个设置可能会有点迷惑。这里填的是一个[正则表达式](http://deerchao.net/tutorials/regex/regex.htm)，目的是匹配job全名（如果使用了Cloudbees Folders Plugin，那么全名还包括为文件夹），具体可以参考[官方文档](https://wiki.jenkins-ci.org/display/JENKINS/Role`Strategy`Plugin#RoleStrategyPlugin-GlobalRolesvs.ProjectRoles)。

#### 全局角色与项目角色
全局角色设定覆盖项目角色的任何设定。那么，如果全局设定某个角色可以`job-read`（如下图设置），那么不管项目角色怎么设定的，这个角色都可以读取任何项目。

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/job.png)

基于此，推荐多数（甚至所有）全局的`Job`，`Run`，`SCM`都不要钩选。

#### 分配角色
回到主页，点击`系统管理`-> `Configure Global Security`-> `Manage and Assign Roles`,进入如下界面。

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/roles.png)

点击`Assign Roles`，首先，在`Global roles`部分，`User/group to add`中输入我们刚才建立的用户名*user1*，点击`add`添加，然后在出现的对应的全局角色位置勾选`employee`。

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/ass.png)

类似的，在`Project roles`位置为*user1*账号添加`developer`角色。

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/proj.png)

这样，我们便添加了全局和项目角色，而且分配了对应用户。

#### slave role暂时没有研究，以后用到了再补充

### 验证新建的角色
首先，我们需要有对应的项目来验证。回到主页，点击`新建`

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/newpro.png)


然后出现下图界面，`Enter an item name`中输入*DevProject1*，然后选择`构建一个自由风格的软件项目`，最后点击`OK`保存。

![]({{ site.url }}/assets/images/posts/CI/2017-02-03-Jenkins-user-setup/enter.png)

现在可以自己试试登录我们的*user1*用户，看看可不可以看到这个项目了。
有兴趣的话，自己多建立几个用户和项目，体验一下这些设置。
