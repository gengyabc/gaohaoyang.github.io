---
layout: post
title:  "2. 初探beautifulsoup"
categories: 爬虫
tags: beautifulsoup 
author: Geng
---

* content
{:toc}

beautifulsoup， 翻译过来就是美丽的汤。
抄一段[官方介绍](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/)如下：
> Beautiful Soup 是一个可以从HTML或XML文件中提取数据的Python库.它能够通过你喜欢的转换器实现惯用的文档导航,查找,修改文档的方式.Beautiful Soup会帮你节省数小时甚至数天的工作时间。这篇文档介绍了BeautifulSoup4中所有主要特性,并且有小例子.让我来向你展示它适合做什么,如何工作,怎样使用,如何达到你想要的效果,和处理异常情况. 文档中出现的例子在Python2.7和Python3.2中的执行结果相同。你可能在寻找 Beautiful Soup3 的文档,Beautiful Soup 3 目前已经停止开发,我们推荐在现在的项目中使用Beautiful Soup 4, 移植到BS4。






本文绝大多数内容来自[官网](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/)，有一些自己的删减和备注。

## 简单了解
继续抄[官方介绍](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/)。**这一章代码我没都有运行过**，有问题不负责任。不过万一运行有问题，不要担心，我在后面会有我自己运行过的代码，这里只是脑子里对它的用法有个了解即可。
下面的一段HTML代码将作为例子被多次用到.这是爱丽丝梦游仙境的一段内容(以后内容中简称为*爱丽丝*的文档):

```python
html_doc = """
<html><head><title>The Dormouse's story</title></head>
<body>
<p class="title"><b>The Dormouse's story</b></p>

<p class="story">Once upon a time there were three little sisters; and their names were
<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
and they lived at the bottom of a well.</p>

<p class="story">...</p>
"""
```

使用BeautifulSoup解析这段代码,能够得到一个 BeautifulSoup 的对象,并能按照标准的缩进格式的结构输出:

```python
from bs4 import BeautifulSoup
soup = BeautifulSoup(html_doc)

print(soup.prettify())
# <html>
#  <head>
#   <title>
#    The Dormouse's story
#   </title>
#  </head>
#  <body>
#   <p class="title">
#    <b>
#     The Dormouse's story
#    </b>
#   </p>
#   <p class="story">
#    Once upon a time there were three little sisters; and their names were
#    <a class="sister" href="http://example.com/elsie" id="link1">
#     Elsie
#    </a>
#    ,
#    <a class="sister" href="http://example.com/lacie" id="link2">
#     Lacie
#    </a>
#    and
#    <a class="sister" href="http://example.com/tillie" id="link2">
#     Tillie
#    </a>
#    ; and they lived at the bottom of a well.
#   </p>
#   <p class="story">
#    ...
#   </p>
#  </body>
# </html>
```

> 注意这个文件名，我一开始取名叫"html.py"，然后一直报错，后来发现是因为这个是**BeautifulSoup**一个文件还是什么的名字，冲突了。

几个简单的浏览结构化数据的方法:
```bash
soup.title
# <title>The Dormouse's story</title>

soup.title.name
# title (源文档是：# u'title'，我不知道什么意思，下面我都改了)

soup.title.string
# The Dormouse's story

soup.title.parent.name
# head

soup.p
# <p class="title"><b>The Dormouse's story</b></p>

soup.p['class']
# title

soup.a
# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>

soup.find_all('a')
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

soup.find(id="link3")
# <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>
```

## 解析器
官方推荐**lxml**，说是速度快，但是网上有人说有坑，那么可以选择**html5lib**。至于怎么选择，详见[官方文档](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id49)：


## 使用方法
首先从名字看，**BeautifulSoup**，是汤。那么想，怎么做汤呢？要有汤料和菜谱吧。对应到爬虫这里，就是要有原始数据和解析方法。原始数据自然就是网页代码，解析方法由上文所述解析器指定。那么，再看上面官网例子(我自己加了解析方法**lxml**)：

```python
from bs4 import BeautifulSoup
soup = BeautifulSoup(html_doc, 'lxml')

print(soup.prettify())
```

可见，原材料是html代码**html_doc**，菜谱是解析方法**lxml**，做出来的汤是**soup**，那就可以开始喝了。

### 对象种类
Beautiful Soup将复杂HTML文档转换成一个复杂的树形结构,每个节点都是Python对象,所有对象可以归纳为4种: **Tag** , **NavigableString** , **BeautifulSoup** , **Comment** 。具体使用见[文档](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/#id11)

总体上，文档大致看下，用到哪里再仔细查看是一个更好的方法。

## 速度
Beautiful Soup对文档的解析速度不会比它所依赖的解析器更快,如果对计算时间要求很高或者计算机的时间比程序员的时间更值钱,那么就应该直接使用**lxml** .

换句话说,还有提高Beautiful Soup效率的办法,使用**lxml**作为解析器.Beautiful Soup用**lxml**做解析器比用**html5lib**或Python内置解析器速度快很多.

安装[cchardet](https://pypi.python.org/pypi/cchardet/)后文档的解码的编码检测会速度更快

解析部分文档 不会节省多少解析时间,但是会节省很多内存,并且搜索时也会变得更快.
