---
layout: post
title:  "JavaScript 1. 介绍"
categories: JavaScript
tags:  JavaScript  
author: Geng
---

* content
{:toc}

**JavaScript**一种直译式脚本语言，是一种动态类型、弱类型、基于原型的语言，内置支持类型。它的解释器被称为**JavaScript引擎**，为浏览器的一部分，广泛用于客户端的脚本语言，最早是在HTML（标准通用标记语言下的一个应用）网页上使用，用来给HTML网页增加动态功能。






在1995年时，由Netscape公司的Brendan Eich，在网景导航者浏览器上首次设计实现而成。因为Netscape与Sun合作，Netscape管理层希望它外观看起来像Java，因此取名为JavaScript。但实际上它的语法风格与Self及Scheme较为接近。

为了取得技术优势，微软推出了JScript，CEnvi推出ScriptEase，与JavaScript同样可在浏览器上运行。为了统一规格，因为JavaScript兼容于ECMA标准，因此也称为**ECMAScript**。

## 为谁而写
不是编程初学者。不是编程初学者。不是编程初学者。

编程初学者请撤离。编程初学者请撤离。编程初学者请撤离。

本教程默认你已经有了编程基础。

## 组成
Javascript由三部分组成：
1. 核心（ECMAScript） 描述了该语言的语法和基本对象
2. 文档对象模型(DOM)描述了处理网页内容的方法和接口
3. 浏览器对象模型(BOM)描述了与浏览器进行交互的方法和接口



![]({{ site.url }}/assets/images/posts/react/native/2017-06-01-JavaScript-basic/bom_dom.jpg)

BOM是浏览器对象模型，用来获取或设置浏览器的属性、行为，例如：新建窗口、获取屏幕分辨率、浏览器版本号等。 DOM是文档对象模型，用来获取或设置文档中标签的属性，例如获取或者设置input表单的value值。 BOM的内容不多，主要还是DOM。 由于DOM的操作对象是文档（Document），所以DOM和浏览器没有直接关系。

DOM针对的是标准的客户端控件，html标记的这些浏览器展现的内容 BOM针对的是浏览器，当然没什么标准

## 特点
* 脚本语言。JavaScript是一种解释型的脚本语言,C、C++等语言先编译后执行,而JavaScript是在程序的运行过程中逐行进行解释。
* 基于对象。JavaScript是一种基于对象的脚本语言,它不仅可以创建对象,也能使用现有的对象。
* 简单。JavaScript语言中采用的是弱类型的变量类型,对使用的数据类型未做出严格的要求,是基于Java基本语句和控制的脚本语言,其设计简单紧凑。
* 动态性。JavaScript是一种采用事件驱动的脚本语言,它不需要经过Web服务器就可以对用户的输入做出响应。在访问一个网页时,鼠标在网页中进行鼠标点击或上下移、窗口移动等操作JavaScript都可直接对这些事件给出相应的响应。
* 跨平台性。JavaScript脚本语言不依赖于操作系统,仅需要浏览器的支持。因此一个JavaScript脚本在编写后可以带到任意机器上使用,前提上机器上的浏览器支 持JavaScript脚本语言,目前JavaScript已被大多数的浏览器所支持。

JavaScript以其跨平台、容易上手等优势大行其道。同时，有些特殊功能（如AJAX）必须依赖Javascript在客户端进行支持。随着引擎如V8和框架如Node.js的发展，及其事件驱动及异步IO等特性，JavaScript逐渐被用来编写服务器端程序。

## 版本
**ECMAScript 6**

ECMAScript 6标准（简称ES6）已经在2015年6月正式发布，它的目标是使得**JavaScript**可以用来编写复杂的大型应用程序，成为企业级开发语言。

### [ES6 与 ECMAScript 2015 的关系](http://es6.ruanyifeng.com/#docs/intro#ES6-与-ECMAScript-2015-的关系)
这部分内容来自 [ES6 与 ECMAScript 2015 的关系](http://es6.ruanyifeng.com/#docs/intro#ES6-与-ECMAScript-2015-的关系)

ECMAScript 2015（简称 ES2015）这个词，也是经常可以看到的。它与 ES6 是什么关系呢？

2011年，ECMAScript 5.1版发布后，就开始制定6.0版了。因此，ES6 这个词的原意，就是指 JavaScript 语言的下一个版本。

但是，因为这个版本引入的语法功能太多，而且制定过程当中，还有很多组织和个人不断提交新功能。事情很快就变得清楚了，不可能在一个版本里面包括所有将要引入的功能。常规的做法是先发布6.0版，过一段时间再发6.1版，然后是6.2版、6.3版等等。

但是，标准的制定者不想这样做。他们想让标准的升级成为常规流程：任何人在任何时候，都可以向标准委员会提交新语法的提案，然后标准委员会每个月开一次会，评估这些提案是否可以接受，需要哪些改进。如果经过多次会议以后，一个提案足够成熟了，就可以正式进入标准了。这就是说，标准的版本升级成为了一个不断滚动的流程，每个月都会有变动。

标准委员会最终决定，标准在每年的6月份正式发布一次，作为当年的正式版本。接下来的时间，就在这个版本的基础上做改动，直到下一年的6月份，草案就自然变成了新一年的版本。这样一来，就不需要以前的版本号了，只要用年份标记就可以了。

ES6 的第一个版本，就这样在2015年6月发布了，正式名称就是《ECMAScript 2015标准》（简称 ES2015）。2016年6月，小幅修订的《ECMAScript 2016标准》（简称 ES2016）如期发布，这个版本可以看作是 ES6.1 版，因为两者的差异非常小（只新增了数组实例的includes方法和指数运算符），基本上是同一个标准。根据计划，2017年6月发布 ES2017 标准。

因此，ES6 既是一个历史名词，也是一个泛指，含义是5.1版以后的 JavaScript 的下一代标准，涵盖了ES2015、ES2016、ES2017等等，而ES2015 则是正式名称，特指该年发布的正式版本的语言标准。本书中提到 ES6 的地方，一般是指 ES2015 标准，但有时也是泛指“下一代 JavaScript 语言”。

## 部署进度
我们可以访问[ES compatibility table](http://kangax.github.io/es5-compat-table/es6/)来查看各个浏览器的支持情况。

建议安装**node**以更好支持ES6。可以使用[阮一峰的es-checker](http://ruanyf.github.io/es-checker)来查看你的node环境的ES6支持情况：

```bash
npm install -g es-checker
es-checker
```

## 克服兼容性
可以使用Babel等工具，具体看[阮一峰介绍](http://es6.ruanyifeng.com/#docs/intro#Babel-转码器)。

## 哪里使用了Javascript
几乎所有网站都使用了Javascript，比如查看页面源代码的类似部分：

```html
<head>
  <script>
    alert('不一定是这个函数，但是是在<script>里面的');
  </script>
</head>
```

或着类似这样：

```html
<head>
  <script src="某个Javascript文件"></script>
</head>
```

上面就是两种常见的在网站中使用Javascript的方法。不过这个系列的重点不是浏览器中运行JavaScript, 所以以后不会见到这些东西了.

## 用什么写Javascript呢？
随便，想用什么看网上介绍吧，挑一个适合自己的。
