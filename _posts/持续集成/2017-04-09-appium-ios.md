---
layout: post
title:  "9. Appium中运行iOS应用"
categories: 持续集成
tags:  Appium
author: Geng
---

* content
{:toc}


安装完了Appium Desktop，也知道了怎么生成ipa和app文件，下一步就是在Appium中测试一个iOS应用了。

Appium Desktop提供了一种运行Appium服务器便捷的方法，而且还可以方便的查看应用。






## Appium Desktop界面介绍

### 启动服务器界面
启动简单配置的服务器：

![](https://github.com/appium/appium-desktop/raw/master/docs/images/screen-start-simple.png)

这个界面是启动Appium Desktop的第一个界面，默认设置了各种参数，一般来说我们可以直接使用这些默认参数，点击`Start Servier`即可。

### 启动复杂配置的服务器

![](https://github.com/appium/appium-desktop/raw/master/docs/images/screen-start-advanced.png)

这里包括所有你可以配置的参数，不过如果不知道什么意思，就不要瞎搞了。

### 使用保存的服务器配置

![](https://github.com/appium/appium-desktop/raw/master/docs/images/screen-start-presets.png)

这里可以使用自定义配置的服务器配置

### 服务器终端界面
点击启动服务器按钮后，进入服务器终端界面：

![](https://github.com/appium/appium-desktop/raw/master/docs/images/screen-logs.png)

这里有Appium服务器的基本信息。这里，你可以点击`Start New Session`启动一个新的测试会话。

### 新会话界面
这里你通过设置参数，可以启动一个Appium会话。这里有很多种Appium服务器供你选择，可以使用Appium Desktop的服务器，也可以使用其他的。

![](https://github.com/appium/appium-desktop/raw/master/docs/images/screen-new-session.png)

因为不一定要使用Appium Desktop自己的服务器，所以可以不启动Appium Desktop服务器直接启动会话。到"File" (Windows/Linux)或者"Appium" (Mac)然后"New Session..."即可。

这里重点介绍`Automatic Servier`，其他自行去[官网](https://github.com/appium/appium-desktop#appium-endpoints)了解。

### Desired Capabilities

**Desired capabilities**设置Appium session的参数。在[caps.md](https://github.com/appium/appium/blob/master/docs/en/writing-running-appium/caps.md)你可以查到各种参数。注意填对参数和值，Appium不会检查。可以点击`+`添加新的。在右侧可以看到JSON格式的文件。如果设置完成，可以点击`Save As...`保存，然后`Saved Capability Sets`可以打开这个配置 。
点击`Start Session`可以启动一个Appium Desktop Inspector。

### 启动Inspector
找一个应用，然后参照上图配置，做少许修改（一般只要修改一下app路径就可以了），就可以启动了。

为了介绍方便，从[苹果官网下载一个应用](https://developer.apple.com/sample-code/swift/downloads/09_PersistData.zip)，然后采用上一节介绍的方法生成一个app文件。
也可以在Xcode中生成app文件：点击`Run`，模拟器启动后, 打开`Products`，然后app文件`Show in Finder`，拖到想要的地方即可。


点击`Start New Session`，开启`Inspector`。启动过程可能比较慢，不要太着急。

![]({{ site.url }}/assets/images/posts/CI/2017-04-09-appium-ios/1.png)

观察上图，最左侧是app当前界面（这个时候模拟器也会启动），中间是XML文档界面结构，右侧是各种信息。
正常来说，你可以点击左侧app的元素来定位某个元素，但是我发现好像效果不太好。不过你可以通过中间XML来定位，只不过麻烦不少。定位之后，观察右侧**Find By**栏，这里是Appium推荐的定位方式。上图中，我定位`add`按钮，Appium推荐的定位方式是**accessibility id**。不过你也可以使用下面的其他方法定位。左侧栏目上面还有交互选项，**Tap**和**Send Keys**，一个是点击，一个是输入。其他界面功能自己试试，应该不难。不过Appium Desktop还有不少bug，所以遇到问题不要认定是自己的问题。
