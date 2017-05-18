---
layout: post
title:  "7. 测试前准备"
categories: 持续集成
tags:  Appium
author: Geng
---

* content
{:toc}


我们已经熟悉了持续集成的总体概念和其核心控制器`Jenkins`的基本使用方法，下面就要开始真正测试的工作了。

首先我们会选取一个合适的测试框架，我们选用了`Appium`。然后介绍`Appium`的安装和基础。最后初步配置一下自动化测试环境

2017.04.09更新 -- appium desktop






## 测试框架选择
目前测试框架有很多，`iOS`有自己的官方测试框架，`Android`也有自己官方的测试框架。为了测试`iOS`和`Android`两种平台，如果我们使用官方框架，势必造成学习成本升高，工作量加大的问题。所以，采用第三方通用测试框架，将是一种比较合适的选择。

第三方框架也有很多，开源的，免费的也不少。比较常用的有两个，一个是[Appium](http://appium.io/)，一个是[Calabash](http://calaba.sh/)。

他们对两种平台的接口基本一致，维护成本低，而且支持常用的`Python`和`Ruby`。

我个人比较喜欢`Calabash`的`cucumber`风格，但是因为使用的人少，不方便后期工作的展开，所以还是选择了`Appium`。

## Appium
它同时支持`iOS`、`Android`、`H5`的测试，我们可以使用一种API针对两种平台写测试，这样的好处就是代码通用性较高，节省测试成本。

`Appium`相对于其他第三方测试框架的一个优点就是它不需要对`App`重新编译。这意味着我们不需要访问源代码。

### Appium哲学
1. 采用`Appium`时，无需对被测应用做任何修改，也无需嵌入任何东西；
2. 不必限制于使用特定编程语言。你可以选择很多语言，比如：Ruby, Python, Java, Node.js, Objective-C, PHP and C#。
3. 不必重新造轮子。`Appium`对`iOS`和`Android`的原生自动化测试框架进行了封装，并提供了统一的API（WebDriver API），减少了自动化测试代码的维护工作量和学习成本；
4. 开源。

`Appium`采用*Client-Server*的架构设计，并采用标准的HTTP通信协议；`Server`端负责与`iOS/Android`原生测试框架交互，无需测试人员关注细节实现；`Client`端基本上可以采用任意主流编程语言编写测试用例，减少了学习成本。

关于Appium的设计哲学及如何实现这些思想，建议阅读这份[官方文档](https://github.com/appium/appium/blob/master/docs/en/about-appium/intro.md)(这个是英文版，虽然有中文翻译版本，但是不够新，所以这里给出的英文版链接)

## 准备测试
Appium最近出了一款新的桌面版本，Appium Desktop。它为`Appium Servier`提供了全新的图形界面，我们以后将主要使用它来进行我们的工作。它包括：
* `Appium Servier`图形界面。内部集成了Node，不再需要单独安装Node。
* 一个`Inspector`，用来查看应用的元素，和应用交互等。

![](https://github.com/appium/appium-desktop/raw/master/docs/images/screen-inspector-and-logs.png)

可以进入[下载页](https://github.com/appium/appium-desktop/releases/)下载。

### 环境要求
关于各种环境的具体要求，随着时间流逝，肯定是不一样的，可以去[官网查看最新的要求](http://appium.io/getting-started.html?lang=en)。
#### iOS环境
在`Appium`中测试`iOS`时，依赖于`Apple`开发环境，因此，在运行`Appium`之前需要先确保如下环境安装正确。
* Mac OS X 10.10 or higher, 10.11.1 recommended
* XCode >= 6.0, 7.1.1 recommended
* Apple Developer Tools (iPhone simulator SDK, command line tools)

#### Android环境
* Android SDK API >= 17 (Additional features require 18/19)
* Appium supports Android on OS X, Linux and Windows.

### 版本选择（和Appium desktop无关）
尤其版本进化速度很快，安装包很可能不能跟上时代，所以建议大家仔细查看安装的版本。现在（2017.03）在`Mac`平台上，最新的安装包是1.5.3，但最新版已经1.6.3了。

为什么不能用旧版呢？因为苹果把测试底层代码改了，旧版还用的是旧的API，不用新版不能测了。。。

> 如果能用安装包，建议还是使用安装包安装，因为它提供一个`Inspector`功能，可以调用模拟器运行被测应用程序，并且可以很方便地在预览页面中查看UI元素的层级结构和详细控件属性，极大地提高编写测试脚本的效率。

### 开始安装

#### 非Appium desktop，安装Appium
根据前面的介绍，`Appium`采用*Client-Server*的架构设计，因此安装`Appium`时需要分别安装`Server`部分和`Client`部分。

首先，如果没有安装`node`，需要先安装`node`（Appium desktop不需要安装这个）
```bash
brew install node
```

使用安装包安装太简单了，我都不好意思写那么多，所以就不写了，不过对于新手，安装吧，不要管是不是最新版，下载你能下载到的最新版就好，好处下一部分会讲到。下面介绍下命令行安装：

```bash
$ npm install -g appium  # 安装 appium server
$ npm install wd         # 安装 appium client (WebDriver/Selenium 2 node.js client)
$ appium                # 启动 appium
```

为了确保环境正确，可以使用`appium-doctor`来确保：
```bash
$ npm install -g appium-doctor    #安装
$ appium-doctor
```

如果安装`$ npm install wd`出现如下错误：
```bash
ENOENT: no such file or directory, open '/Users/yourname/package.json'
npm WARN EPACKAGEJSON /Users/yourname No description
npm WARN EPACKAGEJSON /Users/yourname No repository field.
npm WARN EPACKAGEJSON /Users/yourname No README data
npm WARN EPACKAGEJSON /Users/yourname No license field.
```

那么首先需要先用`npm init`初始化生成`package.json`文件，再安装。

安装完后，使用`appium-doctor`来确定环境设置合适
```bash
$ npm install -g appium-doctor
$ appium-doctor
```

使用`appium-doctor`来进行验证环境

一般可能会有java和Android的一些配置问题，iOS方面很少出现问题，比如：
```bash
WARN AppiumDoctor  ✖ ANDROID_HOME is NOT set!
WARN AppiumDoctor  ✖ JAVA_HOME is NOT set!
WARN AppiumDoctor  ✖ adb could not be found because ANDROID_HOME is NOT set!
WARN AppiumDoctor  ✖ android could not be found because ANDROID_HOME is NOT set!
WARN AppiumDoctor  ✖ emulator could not be found because ANDROID_HOME is NOT set!
```

可以参考下面配置，将它们写入你的".bashrc"：
```bash
export PATH=${JAVA_HOME}/bin:$PATH

export JAVA_HOME=$(/usr/libexec/java_home)
export ANDROID_HOME=/Users/你的名字/Library/Android/sdk
```

最后结果如果是下面这样，说明一些环境准备就绪了：
```bash
info AppiumDoctor ### Diagnostic completed, no fix needed. ###
info AppiumDoctor
info AppiumDoctor Everything looks good, bye!
info AppiumDoctor
```

#### Appium desktop安装
直接下载最新的版本，安装即可。Mac版本记得将应用拖入Application文件夹。
如果有问题，可以采用上一部分介绍的使用`appium-doctor`方法检查问题。


### 客户端测试语言及安装
Appium项目已经针对众多主流的编程语言，将底层协议处理相关的工作封装为`Library`，通过调用这些`Library`，可以极大地简化我们编写测试用例的工作量。
选定编写测试用例的语言后，我们就可以针对性地进行安装`Library`。

首先，我们今后会涉及到Python和ruby。如果没有安装，运行下列命令（这些不是很好的安装这两种语言的方法，想要了解怎么样更好，请自行搜索）：

```bash
brew install python
brew install python3
brew install ruby
```

如果选择`Ruby`语言，那么需要安装的`Library`就是`appium_lib`，安装方式如下：

```bash
$ gem install appium_lib
```

如果选择`Python`语言，那么需要安装的`Library`就是`Appium-Python-Client`，安装方式如下：
```bash
$ pip install Appium-Python-Client
```

对于其它编程语言，请自行参考[官方文档](http://appium.io/)。
