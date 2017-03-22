---
layout: post
title:  "6. Jenkins教程 -- 与git和github集成"
categories: 持续集成
tags:  Jenkins
author: Geng
---

* content
{:toc}

我们已经掌握了Jenkins的基础内容，这部分介绍一个基本的与git集成的方法
我们会：
1. 新建一个java程序并且使用命令行运行它
2. 新建一个Jenkins的job来运行这个java程序
3. 将这个程序加入git与github
4. 中添加git插件
5. 当github出现变化时，启动Jenkins的job




## 准备 -- 新建一个java程序
如果有其他程序用，也可以，这里仅仅是为了方便讲解，所以新建一个java程序"HelloWorld":

```java
public class HelloWorld
{
  public static void main(String[] args) {
    System.out.println("大家好");
  }
}
```
命令行中，我们可以如下运行它：

```bash
javac HelloWorld.java 
java HelloWorld 
```

## 新建Jenkins的job
方法同上一部分，新建一个名为“HelloWorld“的项目（job）

设定构建选项。进入项目配置，找到`构建`，因为我用的Mac，所以选择”Execute shell“，如果是win用户，请选择第一个选项。

![]({{ site.url }}/assets/images/posts/2017-02-05-Jenkins-git-github/2.png)

`Command`框中，输入：

```bash
cd /你的路径/
javac HelloWorld.java 
java HelloWorld 
```
注意“你的路径”输入绝对路径，防止出错。保存设置。

然后测试一下，点击`立即构建`，构建完成后，点击这次构建的历史记录：

![]({{ site.url }}/assets/images/posts/2017-02-05-Jenkins-git-github/b.png)

然后点击`Console Output`，我们便可以看到控制台输出结果了，说明目前我们此job配置正确。

## 将这个程序加入git与github（与Jenkins无关，如果知道如何操作，请忽略）
### 加入git
如果git不知道怎么使用，请先查看[Git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)或者[GIT IMMERSION](http://gitimmersion.com/)

在与HelloWorld.java同目录中，输入：
```bash
$ git init
$ git add .
$ git commit -m "HelloWorld init"
```
### github设置
此处参考[Generating a new SSH key and adding it to the ssh-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/#generating-a-new-ssh-key), [Adding a new SSH key to your GitHub account](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)

我这里的路程针对mac系统
#### 建立一个SSH key]
1. 打开终端
2. 把下面命令粘贴进去，邮箱改为你自己的github账号邮箱。这样就会建立一个新的ssh key
```bash
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

3. 如果提示 "Enter a file in which to save the key," 点击`Enter`，使用默认位置 
```bash
Enter a file in which to save the key (/Users/you/.ssh/id_rsa): [Press enter]
```

4. 因为我们在使用CLI部分已经创建过一个ssh key，所以这里可以将以前的删除，也可以直接覆盖。这里我就直接覆盖了。如果提示“Overwrite (y/n)?”，直接打入"y"，覆盖即可。

5. 然后输入passphrase。完成第一步
```bash
Enter passphrase (empty for no passphrase): [Type a passphrase]
Enter same passphrase again: [Type passphrase again]
```

#### 使用ssh-agent
为了不用每次和github交流都输入密码，而且还能保证安全，我们使用ssh-agent

1. 为了添加ssh-key到the ssh-agent，ssh-agent必须运行。OS X Leopard及以上版本ssh-agent自动运行。如果没有运行
``` bash
# start the ssh-agent in the background if it's not already running
$ eval "$(ssh-agent -s)"
Agent pid 59566
```

2. 添加SSH key到the ssh-agent.
``` bash
$ ssh-add ~/.ssh/id_rsa
```

#### 将SSH key加入你的GitHub账号
1. 复制SSH key到剪切板
```bash
$ pbcopy < ~/.ssh/id_rsa.pub
# Copies the contents of the id_rsa.pub file to your clipboard
```

> 如果pbcopy不工作，那么直接打开`~/.ssh/id_rsa.pub`，复制即可

2. github右上角用户头像位置，点击出现下拉菜单，点击`Settings`

![]({{ site.url }}/assets/images/posts/2017-02-05-Jenkins-git-github/git.png)

点击`New SSH Key`，然后`Title`中填入你自己给这个key起的名称方便记忆和分辨。`Key`中粘贴复制的key，最后点击“Add SSH Key`即可。

![]({{ site.url }}/assets/images/posts/2017-02-05-Jenkins-git-github/ti.png)

### 将java程序上传至github
登录github后，页面右上角，点击`New repository`

![]({{ site.url }}/assets/images/posts/2017-02-05-Jenkins-git-github/new.png)

`Repository name`中输入“HelloWorld”，然后点击`Create Repository`，然后根据提示，输入命令即可，比如：
```bash
$ git remote add origin git@github.com:gengyabc/HelloWorld.git
$ git push -u origin master
```
转到github此项目主页，可见文件已经上传成功

## Jenkins与github集成
首先确定git插件有没有安装，`系统管理` -> `管理插件` -> `已安装`,查看有没有git插件，没有的话，转到`可选插件`， `Filter:`位置搜索“Git plugin”然后安装即可（新版Jenkins内置安装）

进入Jenkins项目的管理页面，找到`源码管理`，点选`Git`，地址填入GitHub项目的git地址（我发现用ssh地址好像不能工作，不知道为什么，这里使用https地址）

![]({{ site.url }}/assets/images/posts/2017-02-05-Jenkins-git-github/gitaa.png)

`构建触发器`，勾选`Poll SCM`，`日程表`填入“* * * * *”(实际工作中不要填入这一堆\*，这里是说每分钟都去查看有没有更新，有更新就拖下来构建，这样会累死Jenkins和服务器`)，然后保存。

![]({{ site.url }}/assets/images/posts/2017-02-05-Jenkins-git-github/bui.png)

修改java项目，然后保存并提交修改到github
```bash
$ git add .
$ git commit -m "I have changed something"
$ git push
```
回到项目主页，等一下，观察构建历史，便有新的构建产生。点击这个历史记录，找到`Polling Log`，可以查看这次构建远程拉取的一些信息。
