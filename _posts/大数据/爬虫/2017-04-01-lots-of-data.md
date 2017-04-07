---
layout: post
title:  "7. 爬取大量数据"
categories: 爬虫
tags: MongoDB PyMongo
author: Geng
---

* content
{:toc}

前面爬虫和MongoDB的基础上，我们已经具备了爬取大量数据的基础，现在要做的就是怎么样把这些各个方面结合起来。

先分析一下我们应该怎么做。假设我们要爬取58同城。
第一步应该找到所有的类目的链接。第二步就是进入这些链接，找到每个商品的链接。最后就是进入各个商品，爬取商品信息了。

注意，由于网站结构等内容在不断更新，所以现在这个爬虫在不远的未来可能就不再适用了。






## 类目链接
58同城的类目可以说是。。。一团乱麻。为了找出想要的类目，注意到类目都在`.colWrap`中的`em a`内，所以我们可以把代码写为：
```python
from bs4 import BeautifulSoup
import requests


start_url = 'http://sz.58.com/'


def get_categories(url):
    wb_data = requests.get(url)
    soup = BeautifulSoup(wb_data.text, 'lxml')
    links = soup.select('.colWrap em a')
    for link in links:
        cat_url = url + link.get('href')
        print(cat_url)
    return categories
```

但是运行上面代码，发现出现了一些奇怪的链接，比如：
"http://sz.58.com//pinpaigongyu/?from=58_pc_zonghe_home_ppgy_wzl_shouyewzl", "http://sz.58.com//ershouche/?minprice=0_3"，“http://sz.58.com/http://www.58che.com/2801/?from=58tc.000316”， “http://sz.58.com//jianzhi.shtml”等。观察这些链接，我们可以发现，一些是因为设置了筛选条件，一些是其他网页，我们可以修改上述代码的构造`cat_url`的语句：
```python
cat_url = url + link.get('href').split('/')[1]
```

可以发现，这些奇怪的链接没有了，但是取而代之的是一些重复的（“http://sz.58.com/ershouche”），或者58同城入口页面（“http://sz.58.com/”）还有部分无关网页（“http://sz.58.com/jianzhi.shtml”）

关于去除重复，我们可以使用*set*来存储。然后我们可以去除所有58同城入口页面链接和`.shtml`结尾的链接:
```python
categories = set()
def get_categories(url):
    wb_data = requests.get(url)
    soup = BeautifulSoup(wb_data.text, 'lxml')
    links = soup.select('.colWrap em a')
    for link in links:
        cat_url = url + link.get('href').split('/')[1]
        if cat_url == start_url:
            continue
        if cat_url.endswith(".shtml"):
            continue
        categories.add(cat_url)
```

采用上述代码后，58同城主页上的类目链接就全部爬取下来了。下一步就是进入各个链接了。

## 商品列表和详情
观察商品列表，其实可以发现，我们已经可以看到商品的主要信息了，所以其实不需要再进入商品详情页了。
由于58同城的结构实在是太可怕了，这里我只爬取一类网页，其他可以采用类似分析方法爬取。我这里爬取类似“http://sz.58.com/danche/”的网页，其商品列表是一个表格。

每个商品在列表中如下展示：

![]({{ site.url }}/assets/images/posts/spider/2017-04-01-lots-of-data/1.png)

这里，我们可以看到有商品的标题，价格，位置信息，我只关心这三个信息，那我就爬取这三个信息就够了。所以就不需要再深入商品详情页了。

观察网页结构，我们关心的所有信息都在各个`<td class="t">`内:
```python
def get_products_of_one_page(url):
    products = list()
    wb_data = requests.get(url)
    soup = BeautifulSoup(wb_data.text, 'lxml')
    if soup.find('div', {"class": "noinfotishi"}):
        raise ValueError("没有了")
    items = soup.findAll("td", {"class": "t"})
    for item in items:
        title = item.find("a").text
        price = item.find("span", {"class": "price"}).text
        location = item.find("span", {"class": "fl"}).text.replace('\n', '')
        product = {"title": title,
                   "price": price,
                   "location": location}
        products.append(product)
    return products
```

这里我是用了Beautifulsoup的`findAll()`方法，这个方法可以返回所有满足条件的结果，而且返回的数据类型是`ResultSet`，方便后面循环中继续查找具体内容。然后单个商品数据保存在一个字典中，最后放入一个数组。

## 分页
现在我们可以进入主页，主页可以进入各个分类的链接了。但是在各个链接里，我们只能爬取第一页的数据，显然这是远远不够的，我们必须让爬虫可以翻页。

观察58同城分页，可以发现，分页类似这样“http://sz.58.com/danche/pn2/”，也就是第x页就是"pnx"。只要有那个分页，那么我们就一直pn下去就可以了。但是怎么结束呢？我们需要一个很大的分页，比如“http://sz.58.com/danche/pn20000/”，会发现提示：“很抱歉，没有找到相关信息”，那么，只要其他页面不存在这个信息或者其CSS特征，就可以根据它来判断是否结束了，然后我们可以建立一个循环，爬取所有分页:
```python
def get_products_of_one_page(url):
    ...
    soup = BeautifulSoup(wb_data.text, 'lxml')
    if soup.find('div', {"class": "noinfotishi"}):
        raise ValueError("没有了")
    items = soup.findAll("td", {"class": "t"})
    ...
    return products


def get_products_of_all_pages(page):
    products = list()
    page_num = 1
    while True:
        try:
            page_products = get_products_of_one_page(page + "pn" + str(page_num))
        except ValueError:
            print("wrong")
            break

        page_num += 1
        products.extend(page_products)

    return products
```

## 爬取所有链接
有了上面的知识，我们可以爬取所有分类信息，并将其保存如MongoDB了：
```python
from bs4 import BeautifulSoup
import requests
import time
import pymongo


# MogoDb设置
client = pymongo.MongoClient('localhost', 27017)
tongcheng_db = client["tongcheng"]
products = tongcheng_db["products"]

# 入口网址
start_url = 'http://sz.58.com/'


def get_categories(url):
    """
    获得页面的类目链接
    :param url: 页面链接
    :return: 链接网址
    """

    categories = set()  # set类型，防止重复链接
    wb_data = requests.get(url)
    soup = BeautifulSoup(wb_data.text, 'lxml')
    links = soup.select('.colWrap em a')
    for link in links:
        cat_url = url + link.get('href').split('/')[1]  # 构造链接
        if cat_url == start_url:  # 如果和入口网址一样，跳出本次循环
            continue
        if cat_url.endswith(".shtml"):  # 如果以.shtml结尾，跳出本次循环
            continue
        categories.add(cat_url)  # 加入set
    return categories  # 返回类目结果set


def get_products_of_all_pages(page):
    """
    获得一个类目内所有的商品信息，并写入数据库
    :param page: 一个类目的第一个页面网址
    """

    def get_products_of_one_page(url):
        """
        或者一个类目某一个页面的商品信息，并写入数据库
        :param url: 页面网址
        """
        wb_data = requests.get(url)
        soup = BeautifulSoup(wb_data.text, 'lxml')
        if soup.find('div', {"class": "noinfotishi"}):  # 没有更多商品了
            raise ValueError("没有了")
        if not soup.find("td", {"class": "t"}):  # 不是我要的商品
            raise ValueError("不爬取这种")
        items = soup.findAll("td", {"class": "t"})
        for item in items:
            try:
                title = item.find("a").text
                price = item.find("span", {"class": "price"}).text
                location = item.find("span", {"class": "fl"}).text.replace('\n', '')
            except AttributeError:  # 如果上面信息有缺失，捕获这个错误
                print(url + "有商品少了些信息")
                continue

            products.insert_one({"title": title,
                                 "price": price,
                                 "location": location})
            print("inserted")
        print("ok")

        time.sleep(1)  # 每个页面爬取完后休息一分钟，防止被封

    page_num = 1
    while True:
        try:
            get_products_of_one_page(page + "/pn" + str(page_num))
        except ValueError as error:
            print(str(error))
            break
        page_num += 1


def start():
    categories = get_categories(start_url)
    print(categories)
    for category in categories:
        get_products_of_all_pages(category)

start()
```

这里，我增加了一些异常处理，并添加了一些备注，但是大体代码没有怎么变化。
