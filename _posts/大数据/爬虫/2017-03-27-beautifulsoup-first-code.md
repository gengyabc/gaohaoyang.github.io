---
layout: post
title:  "3. beautifulsoup爬我的博客"
categories: 爬虫
tags: beautifulsoup 
author: Geng
---

* content
{:toc}

上一章我们已经了解到了beautifulsoup的基本知识，那么这一章，就用beautifulsoup真实的爬取一个网站。由于外部环境的复杂性，或者反爬虫机制，或者其他原因，爬取一个真实的网站会有各种不稳定的问题。为了既能真实，又能稳定，我觉得就做一个爬取我自己博客信息的小爬虫。






## 定位目标
为了爬取数据，首先要定位目标。那么我们的目的很简单，就是进入[主页](http://wanwu.tech/)，然后爬取主页的各个文章的题目，发布日期，分类，标签和简介内容。

### 根据自己的CSS类来定位
根据上一章内容，我们可以通过标签和CSS类来定位目标。在我们这个场景中，标签显然是不合适的，那么我们自然选用CSS类来定位。

首先查看各个目标的CSS类特征。我使用Chrome浏览器来查看。假设我要寻找标题特征，我可以鼠标放在标题上，右键选择`查看`（inspect）（我的浏览器是英文版，中文是我自己的翻译，有可能会有出入，请见谅），我们会发现下面部分高亮：

```html
<a class="post-link" href="/2017/03/26/beautifulsoup-intro/">2. 初探beautifulsoup</a>
```

这里我们可以发现，类就是*post-link*。为了确定这个类可以而且只会定位我们要的标题，可以在页面右键，选择`查看页面源代码`，然后搜索*post-link*。可以发现，*post-link*可以而且只定位了几个标题。

类似方法，定位文章简介内容，使用类*excerpt*。

### 根据兄弟节点的类定位
然后我们观察发布日期，他的信息内容本身没有CSS类!那怎么办呢？仔细观察日期：
```html
<div class="label-card">
   <i class="fa fa-calendar"></i>
   2017-03-03
</div>
```

我们可以发现，日期的上一个兄弟标签`i`的CSS类有可以定位的*fa-calendar*。

### 根据元素属性定位
然后观察分类，它也没有自己的累，但是却有特殊的标签属性`rel="category"`:
```html
<a href="/category/#使用Swift学编程" title="Category: 使用Swift学编程" rel="category">使用Swift学编程</a>
```

由此，我们可以根据这个属性来定位。

### 选择一个合适的定位方式
最好，我们看下标签：
```html
<span class="pageTag">
  <i class="fa fa-tags"></i>
    
  <a href="/tag/#swift" title="Tag: swift" rel="tag">swift</a>&nbsp;
    
  <a href="/tag/#playground" title="Tag: playground" rel="tag">playground</a>
        
</span>
```

观察发现，我们可以根据父标签定位，兄弟标签定位，也可也根据自己的属性定位，那么我们考虑代码的具体实现，再来决定。

## 数据结构
分析我们的目标数据，总体上是**属性名**和其**属性值**的配对，那么我们可以使用字典数据类型。其中，除了标签，属性值都是单一内容的文字。而标签很可能是多个词，如上面代码就包括*swift*和*playground*，那么这个时候，属性值就应该是**列表**型的了。那么，为了将大于一个的属性值可以方便的放在一起，我们使用标签的父元素的类来定位更合适一些。如果不是很明白，直接看后面代码就会明白了。

## 开始写代码

### 准备解析
```python
from bs4 import BeautifulSoup
import requests

url = 'http://wanwu.tech'
web_data = requests.get(url)
soup = BeautifulSoup(web_data.text, 'lxml')
```

首先导入需要的库，*BeautifulSoup*和*requests*。

然后，我们使用*get*方法，连接目标网站。最后，创建一个BeautifulSoup的对象，以备解析。BeautifulSoup的初始化方法各参数定义如下:
```python
__init__(self, markup="", features=None, builder=None,parse_only=None, from_encoding=None, exclude_encodings=None,**kwargs)
```

可见，第一个参数是网页源码，第二个是解析方法。

### 定位目标
下面，我们使用刚才讨论的结论，定位目标：
```python
titles = soup.select('.post-link')
time_siblings = soup.select('.fa-calendar')
cats = soup.select('a[rel="category"]')
tag_parents = soup.select('.pageTag')
excerpts = soup.select('.excerpt')
```
他们都使用了`select`方法，除了分类，都是CSS类定位，标签使用的属性定位。而且，时间和标签分别使用的是兄弟和父级定位。

### 查看数据
我们已经定位了目标，下面试着查看一下我们爬取得信息：
```python
for title, excerpt, time_sibling, cat_sibling, tag_parent in zip(titles, excerpts, time_siblings, cats, tag_parents):
    data = {
        "title": title.get_text(),
        "excerpt": excerpt.get_text().replace('\n', ''),
        "time": time_sibling.next_sibling,
        "cat": cat_sibling.get_text(),
        "tags": list(tag_parent.stripped_strings)
    }
    print(data)
```

这里，使用for循环来逐个打印结果。

首先看下`zip`方法文档：
> `zip(iter1 [,iter2 [...]]) --> zip object`
> Return a zip object whose .***next***() method returns a tuple where the i-th element comes from the i-th iterable argument.  The .***next***() method continues until the shortest iterable in the argument sequence is exhausted and then it raises StopIteration.

简单来说，就是将iterable数据的元素一个一个取出来组成元组。

然后主要使用了get_text()方法获得文本值。其中“excerpt”使用`replace('\n', '')`将多余的换行符去掉。

关于时间。我们只定位了它的上一个兄弟节点，现在，我们使用`next_sibling`来定位它。类似的，还有一个对应的`previous_siblings`。从名字可知它们的作用。

关于标签。我们定位的是它的父节点，然后我们使用`stripped_strings`。

查看[官方文档](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#strings-and-stripped-strings)：
> If there’s more than one thing inside a tag, you can still look at just the strings. Use the .strings generator:

> These strings tend to have a lot of extra whitespace, which you can remove by using the .stripped_strings generator instead:

可见，如果一个HTML标签中有多个内容，那么可以使用".strings generator"。但是它会返回大量的空白，那么我们可以使用“.stripped_strings generator”来去除这些空白。最后我们使用`list`新建一个列表。

观察输出的数据，判断是否符合要求。如果符合，那么我们考虑这些数据存在一个列表中。

## 最终代码

```python
from bs4 import BeautifulSoup
import requests

url = 'http://wanwu.tech/page2/'
web_data = requests.get(url)
soup = BeautifulSoup(web_data.text, 'lxml')

titles = soup.select('.post-link')
time_siblings = soup.select('.fa-calendar')
cats = soup.select('a[rel="category"]')
tag_parents = soup.select('.pageTag')
excerpts = soup.select('.excerpt')

results = []
for title, excerpt, time_sibling, cat_sibling, tag_parent in zip(titles, excerpts, time_siblings, cats, tag_parents):
    data = {
        "title": title.get_text(),
        "excerpt": excerpt.get_text().replace('\n', ''),
        "time": time_sibling.next_sibling,
        "cat": cat_sibling.get_text(),
        "tags": list(tag_parent.stripped_strings)
    }

    results.append(data)

for result in results:
    print(result)
```


## 分页
分页比较简单，就是找到所有页面的网址，然后遍历就可以了。这里不再浪费时间。
