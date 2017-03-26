---
layout: post
title:  "1. 爬虫准备"
categories: 爬虫
tags:  python
author: Geng
---

* content
{:toc}

近期因为项目原因，需要搞搞爬虫了，那么我就随大流，选择python了。不过我自己也是python新手，所以边学边用了。

首先给自己科普一下python如何安装，然后记录一些python的基础知识，版本管理之类的问题，然后根据学习进度，一步一步记录工作进展。

整个项目使用爬虫爬取某些网站内容，然后进行分析，最后得出一些分析结果。






## 安装python
目前我使用mac系统，那么我主要针对mac作说明。

### 选择版本
python的版本问题，对于我这样的初学者看来简直就是神经病一样的存在。不过，也只能逆来顺受了。

首先，我也不知道我以后用什么版本，真的不知道。那么我的办法很简单，都装。系统已经预装了python的2.6版本，那么经过搜索，我找到了**pyenv**这个大杀器。

### pyenv
[官方github主页](https://github.com/pyenv/pyenv)介绍：
> This project was forked from rbenv and ruby-build, and modified for Python.

看到**rbenv**，心里逐渐感到一丝暖意，好歹我也学过ruby啊。脑补一下，好像大概知道了一些。不过还是做下笔记吧。简单来说，它的功能就是然你可以装多个版本的python，使用的时候，想用哪个用哪个。

#### 功能
其功能为（懒得翻译了，实在看不懂的话，这里还有一个[小杀器](https://translate.google.cn/#en/zh-CN/Let%20you%20change%20the%20global%20Python%20version%20on%20a%20per-user%20basis.%0AProvide%20support%20for%20per-project%20Python%20versions.%0AAllow%20you%20to%20override%20the%20Python%20version%20with%20an%20environment%20variable.%0ASearch%20commands%20from%20multiple%20versions%20of%20Python%20at%20a%20time.%20This%20may%20be%20helpful%20to%20test%20across%20Python%20versions%20with%20tox.)）：
* Let you change the global Python version on a per-user basis.
* Provide support for per-project Python versions.
* Allow you to override the Python version with an environment variable.
* Search commands from multiple versions of Python at a time. This may be helpful to test across Python versions with tox.

#### 原理
其原理其实就是修改系统环境变量**PATH**。**pyenv**在**PATH**前面插入：
```bash
$(pyenv root)/shims:/usr/local/bin:/usr/bin:/bin
```

那么在你知性python相关命令的时候，因为系统寻找命令是从左到右查看**PATH**，他就会首先看到你插入的东西。这样，pyenv就可以通过控制**shims**目录中的python版本。

### 安装
```bash
$ brew install pyenv
```

然后修改环境变量，在**bashrc**或者**zshrc**中添加：
```bash
$ eval "$(pyenv init -)"
```

然后使其生效：
```bash
$ source ~/.zshrc
或者
$ source ~/.bashrc
```

### 使用介绍
第一步，先看看帮助：
```bash
$ pyenv -h    
Usage: pyenv <command> [<args>]

Some useful pyenv commands are:
   commands    List all available pyenv commands
   local       Set or show the local application-specific Python version
   global      Set or show the global Python version
   shell       Set or show the shell-specific Python version
   install     Install a Python version using python-build
   uninstall   Uninstall a specific Python version
   rehash      Rehash pyenv shims (run this after installing executables)
   version     Show the current Python version and its origin
   versions    List all Python versions available to pyenv
   which       Display the full path to an executable
   whence      List all Python versions that contain the given executable

See `pyenv help <command>' for information on a specific command.
For full documentation, see: https://github.com/yyuu/pyenv#readme
```

下一步，看看能装什么：
```bash
pyenv install --list
```
会出现一大堆东西。

想要安装某个版本：
```bash
$ pyenv install 3.6.0
$ pyenv install 2.7.13
```

再看看装了什么：
```bash
$ pyenv versions
* system (set by /Users/me/.pyenv/version)
  2.7.13
  3.6.0
```

总体来说，我觉得用新的版本比较好，不然为什么要有新的，那么我可以修改**全局版本**：
```bash
$ pyenv global 3.6.0
```

再看看效果：
```bash
$ pyenv versions
  system
  2.7.13
* 3.6.0 (set by /Users/me/.pyenv/version)
```

可以看出，星号变了位置，说明现在版本是**3.6.0**。

那如果有一天，一个项目我需要用**2.7.13**，但是我不想修改全局呢，那么可以修改**当前目录版本**：
```bash
$ pyenv local 2.7.13
```

可以这样查看当前版本：
```bash
$ pyenv version 
2.7.14 (set by /Users/me/.pyenv/version)
```

注意当前目录会有一个“.python-version”文件，指定当前目录的版本。

### pyenv-virtualenv
我不知道具体有啥好处，不过pyenv主页上挂出来了，看着好像不错，我就现装了。

```bash
$ brew install pyenv-virtualenv
```

然后修改环境变量，在**bashrc**或者**zshrc**中添加：
```bash
$ eval "$(pyenv virtualenv-init -)"
```

其他看[官方文档](https://github.com/pyenv/pyenv-virtualenv#using-pyenv-virtualenv-with-pyenv)吧，我理解不够，写多了怕错。

## 安装需要的库
既然是爬虫，那先选一个容易上手的尝尝鲜，能力不够，先喝汤。安装[Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/)

这里是其[官方文档](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)，可喜的是，有[中文版](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/)。

怎么安装呢：
```bash
pip install beautifulsoup4
```

安装完成，很简单吧。

至于**pip**是什么呢？简单地说，就是一个python包管理器。




