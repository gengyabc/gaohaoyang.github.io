---
layout: post
title:  "8. 使用命令行构建Xcode项目"
categories: 持续集成
tags:  xcodebuild
author: Geng
---

* content
{:toc}


测试环境已经搭建好了，那么下一步的工作，自然就该开始测试了。但是为了测试，我们需要先把应用跑起来。这个步骤每个iOS开发者可能都做过，就是点击Xcode的*run*，或者*cmd+R*，然后模拟器就启动了。那么这中间都发生了什么呢？

这次的内容，主要讨论从点击*run*到启动模拟器之前的部分，是为以后命令行运行`iOS`应用做准备，为以后*Jenkins*启动应用打下基础。关于`Android`，将会在以后讲述。

内容很基础，但是坑也不少。

欢迎入坑👻






## iOS应用安装包的基础知识
详见[iOS应用安装包的基础知识](http://debugtalk.com/post/build-app-automated-test-platform-from-0-to-1-Appium-inspector-iOS-simulator/)

上面参考文献总结出以下几点：
1. 不管是从Apple Store或iTunes上下载的应用，还是在Xcode中针对真机设备编译生成的*.ipa*文件，都是面向于ARM处理器的iOS设备，只能在真机设备中进行安装；
2. 而在Mac OSX系统中运行的iOS模拟器，运行环境是基于Intel处理器的；
3. 因此，若是针对真机设备编译生成的*.ipa*文件，是无法在iOS模拟器中正常运行的，毕竟处理器架构都不一样；
4. 要想在iOS模拟器中运行应用，则必须在Xcode中编译时选择模拟器类型；编译生成的文件后缀为*.app* 。

## 构建方式
详见[关于持续集成打包平台的Jenkins配置和构建脚本实现细节](http://debugtalk.com/post/iOS-Android-Packing-with-Jenkins-details/)。

这里画一下上面文献的重点：
构建方式主要包括两种：
* 源码 -> `.archive`文件 -> `.ipa`文件
* 源码 -> `.app`文件 -> `.ipa`文件
  这两种方式的主要差异是生成的中间产物不同，对应的，两种构建方式采用的命令也不同。

我们重点关注第二种方法，那么命令为：

源码 -> `.app`文件
```bash
# build .app file from source code
xcodebuild \    # xctool
  -workspace ${WORKSPACE_PATH} \
  -scheme ${SCHEME} \
  -configuration ${CONFIGURATION} \
  -sdk ${SDK}
  -derivedDataPath ${OUTPUT_FOLDER}
```

`.app`文件 -> `.ipa`文件
```
```bash
# convert .app file to ipa file
xcrun \
  -sdk iphoneos \
  PackageApplication \
  -v ${OUTPUT_FOLDER}/Release-iphoneos/xxx.app \
  -o ${OUTPUT_FOLDER}/Release-iphoneos/xxx.ipa
```
命令比较复杂，先放在这里，知道就行了，我们下面会仔细分析。

## 准备.app文件
有了这个文件，才能方便应用运行在模拟器，或者进一步转成`.ipa`文件运行在真机。

### 构建
点击Xcode的*run*，就是一个**构建**然后启动模拟器的过程。

首先我们需要了解**构建**到底发生了什么。那么，我们要理解至少下面三个东西：[^1]

`Targets`（目标产物）：构建完成后的单一产物。
`Configuration`（设置）：构建过程中的一套定义。Xcode默认一个新的项目有`RELEASE`和`DEBUG`两种`configuration`，你也可以建立更多的，比如`STAGING`。
`Schemes`（方案）：它将所有东西集合在一起。使用它，你可以指定构建的目标产物，设置，还可以定义不同的针对`Run`，`Testing`，`Analyzing`和`Archiving`的值。Xcode默认创建一个`Schemes`。它设置`Archive`为`RELEASE`，其他都是`DEBUG`

下一步，开启Xcode，查看这三个分别都是什么。
<br>

开启Xcode后，我们可以看到，在*run*右侧，我标注了一个红框，点开它，我们可以看到，和`Schemes`相关。点击这个图标，下拉菜单中，点击`Manage Schemes`，可以看到，`Schemes`与项目名称相同，而且你还可以建立新的`Schemes`。

![]({{ site.url }}/assets/images/posts/CI/2017-04-08-build-ios-by-cli/1.png)


上图标记的另外一个位置，就是`Targets`了。我们开发的时候，经常会在这里修改一些参数，但是可能一直没有留意，这些都是`Targets`内的参数。如果你在新建项目的时候，勾选了测试相关内容，这个位置也会出现测试想关的`Targets`。

<br>

那么`Configuration`在哪里呢？如下图所示，点击项目名称，然后*info*，会看到`Configuration`选项。可以看到，有上文所说的`RELEASE`和`DEBUG`两种。

![]({{ site.url }}/assets/images/posts/CI/2017-04-08-build-ios-by-cli/2.png)

<br>

如果我们想要使用命令来构建，就需要设置上面三个参数。那么，使用什么命令呢？

### xcodebuild
可以使用`xcodebuild`来构建。xcodebuild 是苹果发布自动构建的工具，可以打包项目或者工程。

我们可以通过`man xcodebuild`命令，来查看其使用方法

> 坑：这个命令的文档最好自己通过man来查看，因为网上的教程，甚至你查到的苹果官方网站上官方文档都是老的，过时的。

```bash
XCODEBUILD(1)             BSD General Commands Manual            XCODEBUILD(1)

NAME
     xcodebuild -- build Xcode projects and workspaces

SYNOPSIS
     xcodebuild [-project name.xcodeproj]
                [[-target targetname] ... | -alltargets]
                [-configuration configurationname]
                [-sdk [sdkfullpath | sdkname]] [action ...]
                [buildsetting=value ...] [-userdefault=value ...]

     xcodebuild [-project name.xcodeproj] -scheme schemename
                [[-destination destinationspecifier] ...]
                [-destination-timeout value]
                [-configuration configurationname]
                [-sdk [sdkfullpath | sdkname]] [action ...]
                [buildsetting=value ...] [-userdefault=value ...]

     xcodebuild -workspace name.xcworkspace -scheme schemename
                [[-destination destinationspecifier] ...]
                [-destination-timeout value]
                [-configuration configurationname]
                [-sdk [sdkfullpath | sdkname]] [action ...]
                [buildsetting=value ...] [-userdefault=value ...]

     xcodebuild -version [-sdk [sdkfullpath | sdkname]] [infoitem]

     xcodebuild -showsdks

     xcodebuild -showBuildSettings
                [-project name.xcodeproj | [-workspace name.xcworkspace -scheme 
schemename]]

     xcodebuild -list [-project name.xcodeproj | -workspace name.xcworkspace]

     xcodebuild -exportArchive -archivePath xcarchivepath -exportPath
                destinationpath -exportOptionsPlist path

     xcodebuild -exportLocalizations -project name.xcodeproj -localizationPath
                path [[-exportLanguage language] ...]
     xcodebuild -importLocalizations -project name.xcodeproj -localizationPath
                path

DESCRIPTION
     xcodebuild builds one or more targets contained in an Xcode project, or
     builds a scheme contained in an Xcode workspace or Xcode project.

                ...
```

使用的时候，进入`name.xcodeproj`所在目录，运行这个命令即可。如果有多个`project`，那么需要使用**-project**来指定是哪一个。默认情况下，她会构建列表中第一个。

#### 基本查询
有几个查询命令大家可以熟悉下：

首先是`xcodebuild -list`，可以看到Target，Schemes和Configurations：
```bash
$ xcodebuild -list
  Information about project "Hello":
    Targets:
        Homepwner
        HomepwnerTests
        HomepwnerUITests

    Build Configurations:
        Debug
        Release

    If no build configuration is specified and -scheme is not passed then "Release" is used.

    Schemes:
        Hello
```

然后就是`xcodebuild -showsdks`，可以看到sdk信息。最后还有一个`xcodebuild -showBuildSettings`，可以了解下。

#### 简单使用
打开一个旧的Xcode项目（project）或者新建一个，安装上面讲的方法，在本目录下，运行：
```bash
xcodebuild -project projectName.xcodeproj -configuration Release
```

如果 build 成功，会看到 ** BUILD SUCCEEDED ** 字样.

<br>

如果失败，会有提示，比如如果账号等信息没有设置好，会有以下提示：
```bash
Check dependencies
Signing for "projectName" requires a development team. Select a development team in the project editor.
Code signing is required for product type 'Application' in SDK 'iOS 10.2'

**** BUILD FAILED ****


The following build commands failed:
	Check dependencies
(1 failure)
```

如果这个失败信息，那么你需要打开Xcode，设置`Bundle Identifier`和`Team`。`Team`使用你的苹果账号登录即可，`Bundle Identifier`要求就是别和其他开发者冲突就可以。

![]({{ site.url }}/assets/images/posts/CI/2017-04-08-build-ios-by-cli/3.png)

成功后，会生成一个`build`文件夹，里面有`Release`文件夹，在其中可以看到`.app`文件。

<br>

尝试下面两个命令：
```bash
$ xcodebuild -project Projectname.xcodeproj -configuration Release -scheme Projectname
 ```

```bash
$ xcodebuild -project Projectname.xcodeproj -configuration Release -scheme Projectname -sdk iphonesimulator
```

查看最后输出的路径不一样，目录结构也不一样。

#### 控制路径
那么我们怎么样指定输出路径呢，可以这样：
```bash
$ xcodebuild -project Projectname.xcodeproj -configuration Release -scheme Projectname -sdk iphonesimulator -derivedDataPath .
```

上面命令中的`.`表示当前目录。观察目录结构。可以找到`.app`文件
如果要编译成`.ipa`，进行上文`xrun`即可。

#### 清除缓存文件
在每次build之后，工程目录下会遗留一些缓存文件，以便下次build时减少编译时间。然而，若因为工程配置错误等问题造成编译失败后，下次再编译时就可能会受到缓存的影响。

因此，比较好的做法是在每次build之前都清理一下上一次编译遗留的缓存文件。

```bash
$ xcodebuild clean
```
但是这个过程不是很清理的不是很干净，所以建议这样做：
```bash
$ xcodebuild -project Projectname.xcodeproj -configuration Release -scheme Projectname clean
```

如果还是觉得不够干净，那么可以这样：
```bash
$ xcodebuild -project Projectname.xcodeproj -configuration Release -scheme Projectname -sdk iphonesimulator -derivedDataPath . clean
```

这样下次就是全新的编译了。

### workspace
上面的过程都是针对project，那么如果是workspace呢，我们把命令修改即可。
```bash
$ xcodebuild -project Workspacename.xcworkspace -configuration Release -scheme Projectname -sdk iphonesimulator -derivedDataPath . 
```

如果有Cocoapod依赖，记得要首先运行：
```bash
$ pod install
```

#### CocoaPods介绍
这部分是扫盲需要，熟悉`CocoaPods`可略过。

一般来说，一个完整的`iOS`应用，很可能会使用一些第三方的库，如果使用[CocoaPods](https://cocoapods.org/)管理的项目，在项目根目录下会包含`Podfile`和`Podfile.lock`文件，里面记录了当前项目依赖的第三方库以及对应的版本号。

如果使用了`CocoaPods`管理的库，那么首先需要有`CocoaPods`。如果没有的话，可以如下安装（注意需要安装ruby最新版，否则会有意想不到的奇妙事情，让你经历什么是从入门到放弃）

安装`CocoaPods`：
```bash
$ sudo gem install cocoapods
```

然后，进入到iOS项目的目录，执行`pod install`命令即可安装当前项目的所有依赖。

```bash
$ cd Project_Folder
$ pod install
```

在依赖安装完成后，正常情况下，就可以在Xcode中编译项目了。

[^1]:http://ravelantunes.com/blog/xcode-build-process/
