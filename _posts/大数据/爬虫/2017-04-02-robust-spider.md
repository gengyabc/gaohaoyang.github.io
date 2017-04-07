---
layout: post
title:  "8. 更健壮地爬取大量数据"
categories: 爬虫
tags: MongoDB PyMongo
author: Geng
---

* content
{:toc}

爬虫会遇到很多问题，有简单的，也有复杂的。这里，我只介绍一些简单的问题，比如异常处理，应对反爬虫等问题。





## 异常处理
前面一章已经有了一些异常处理，这里再补充一些常见的异常：
1. 网页没有找到或者获取异常
2. 服务器没有找到
3. 没有爬取得内容

第三点上次内容已有涉及，下面仅说明下前两种情况怎么办。


### 网页没有找到或者获取异常
这种情况，会有HTTP错误返回，可能是“404 Page Not Found”，也可能是“500 Internal Server Error”等。这些情况下，`aise_for_status()`方法会抛出异常。

参考[这段代码](http://stackoverflow.com/questions/8262275/urllib2-try-and-except-on-404)，运行代码，看看效果。可以发现，如果有错误会打印提示，而不是直接崩掉。
```python
import requests
from requests.exceptions import HTTPError

try:
    r = requests.get('http://httpbin.org/status/200')
    r.raise_for_status()
except HTTPError:
    print('Could not download page')
else:
    print(r.url, 'downloaded successfully')

try:
    r = requests.get('http://httpbin.org/status/404')
    r.raise_for_status()
except HTTPError:
    print('Could not download', r.url)
else:
    print(r.url, 'downloaded successfully')
```    


### 服务器没有找到
如果服务器根本就没有找到，比如你输入了”www.meiyou.zhenmeiyou“，但是这个网址不存在。你可以使用下面方法设定timeout时间，超过时间就会报错：
```python
r = requests.get('https://github.com', timeout=5)
```

## 应对反爬虫
关于反爬虫，可以看[这里](http://blog.csdn.net/u013886628/article/details/51820221)。

对于一般情况，我们只要知道下面两种机制即可：

一个就是计算单个userAgent访问频率，如果超过阈值，就封掉。

另一个是限制ip访问频率的反爬虫机制，也就是同一个ip，超过一个访问频率阈值就封掉，可以通过暂停（time.sleep，上一章已经使用过）或者代理方法解决。

### 应对userAgent封锁
在requests的请求的头中存在着User-Agent，我们可以通过下图方法看到它：

![]({{ site.url }}/assets/images/posts/spider/2017-04-02-robust-spider/1.png)

如果一个网站限制同一个User-Agent的频繁访问，或者不允许没有User-Agent的访问，那么我们可以先建立一个User-Agent仓库，然后随机从里面拿出一个用。

首先，我们可以上网找一个这样的仓库，比如[这里](https://techblog.willshouse.com/2012/01/03/most-common-user-agents/)。我们可以把这些User-Agent赋值给一个字符串(为节省篇幅，省略大部分)：
```python
user_agents = '''Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
...
Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.133 Safari/537.36'''
print(user_agents.split('\n'))
```
观察输出，确认没有问题。上面代码将一个字符串以“\n”为分隔符，转成了列表。

那么为了随机使用一个User-Agent，可以采用下面操作：
```python
random.seed()
user_agent = random.choice(user_agents.split('\n'))
headers = {'User-Agent': user_agent}
response = requests.get(url, headers=headers)
```

上面代码中，首先我们使用`random.seed()`初始化一个随机生成器，然后随机选择一个User-Agent。最后将这个随机的User-Agent使用在`requests.get()`中。

这样，我们就可以每次使用随机的一个User-Agent来爬取数据了。

### 应对ip封锁
下面说下如何破解限制ip访问频率的反爬虫机制，我们已经通过暂停（time.sleep）方法降低了访问频率。但是这种方法杀人一千，自损八百，用的过分的话会极大影响爬取速度。

那么我们可以采用设置代理的方法，让别人帮我们访问。这样，我们需要一个比较大的代理池，可以使用“[http://haoip.cc/tiqu.htm](http://haoip.cc/tiqu.htm)”提供的代理。为了使用这个这个站点的最新代理，我们首先需要爬取这个网站的代理列表。

爬取很简单，关键是处理一下数据。爬取得原始数据包含很多空格，回车等空白字符，需要将他们丢弃，代码如下：
```python
def get_ips(url):
    ips = list()
    wb_data = requests.get(url)
    soup = BeautifulSoup(wb_data.text, 'lxml')
    raw_ips = soup.select("div.col-xs-12")[0].text.replace(' ', '').split('\n')
    for raw_ip in raw_ips:
        if raw_ip == '':
            pass
        else:
            ips.append(raw_ip)

    return ips
```

然后就可以在建立连接的时候使用了：
```python
random.seed()
ip = random.choice(ips)

proxy = {'http': ip}  # 构造成一个代理
response = requests.get(url, proxies=proxy)  # 使用代理获取response
```

为了使用方便，我们还可以设置什么时候使用代理，使用代理的规则等内容：
```python
def get_response(url, use_proxy=True, num_retrials=4):
    """
    获取url的响应。可以设置代理和重试次数
    :param url: 要连接到网址
    :param proxy: 代理
    :param num_retrials: 重试次数
    :return: 响应
    """


    def random_ip():
        """
        get random ip from an ip list
        """

        def get_ips(url='http://haoip.cc/tiqu.htm'):
            """
            find the ip list shown in url
            """
            ips = list()
            wb_data = requests.get(url)
            soup = BeautifulSoup(wb_data.text, 'lxml')
            raw_ips = soup.select("div.col-xs-12")[0].text.replace(' ', '').split('\n')
            for raw_ip in raw_ips:
                if raw_ip == '':
                    pass
                else:
                    ips.append(raw_ip)

            return ips

        ips = get_ips()
        random.seed()
        ip = random.choice(ips)
        return ip

    if not use_proxy:  # 如果不使用代理
        try:
            response = requests.get(url)
            response.raise_for_status()
        except HTTPError:  # 如果上面的代码执行报错
            if num_retrials > 0:  # num_retrials是重试次数
                time.sleep(10)  # 延迟十秒
                print('获取网页出错，10S后将获取倒数第：', num_retrials, '次')
                return get_response(url, False, num_retrials - 1)  # 调用自身 并将次数减1
            else:
                print('开始使用代理')
                return get_response(url, True)  # 代理不为空的时候
        else:
            return response

    else:  # 如果使用代理
        ip = random_ip()
        proxy = {'http': ip}  # 构造一个代理
        try:
            response = requests.get(url, proxies=proxy)  # 使用代理获取response
        except HTTPError:
            if num_retrials > 0:
                time.sleep(10)
                print('将会更换代理，10S后将重新获取倒数第', num_retrials, '次')
                return get_response(url, True, num_retrials - 1)
            else:
                print('代理也不好使了！取消代理')
                return get_response(url, False)
        else:
            print('当前代理是：', proxy)
            return response

get_response('http://www.baidu.com', False)
```

上面代码代理设置的部分我已经加上了很多注释，理解起来应该问题不大。

## 综合使用
掌握了前面内容，下面就把所有内容综合起来，搞一个健壮的爬虫出来，而且把数据存入MongoDB：
```python
import random

from bs4 import BeautifulSoup
import requests
import time
import pymongo
from multiprocessing import Pool

# MogoDb设置
client = pymongo.MongoClient('localhost', 27017)
tongcheng_db = client["tongcheng"]
products = tongcheng_db["products"]

# 入口网址
start_url = 'http://sz.58.com/'

user_agents = '''Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_3) AppleWebKit/602.4.8 (KHTML, like Gecko) Version/10.0.3 Safari/602.4.8
Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (Windows NT 10.0; WOW64; rv:52.0) Gecko/20100101 Firefox/52.0
Mozilla/5.0 (Windows NT 6.1; WOW64; rv:52.0) Gecko/20100101 Firefox/52.0
Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; rv:11.0) like Gecko
Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:52.0) Gecko/20100101 Firefox/52.0
Mozilla/5.0 (Windows NT 6.3; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (Windows NT 10.0; WOW64; rv:51.0) Gecko/20100101 Firefox/51.0
Mozilla/5.0 (Windows NT 6.1; WOW64; rv:51.0) Gecko/20100101 Firefox/51.0
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.79 Safari/537.36 Edge/14.14393
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (Macintosh; Intel Mac OS X 10.12; rv:52.0) Gecko/20100101 Firefox/52.0
Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.110 Safari/537.36
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.98 Safari/537.36
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.110 Safari/537.36
Mozilla/5.0 (Windows NT 6.3; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.98 Safari/537.36
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/602.4.8 (KHTML, like Gecko) Version/10.0.3 Safari/602.4.8
Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.98 Safari/537.36
Mozilla/5.0 (X11; Linux x86_64; rv:45.0) Gecko/20100101 Firefox/45.0
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_4) AppleWebKit/603.1.30 (KHTML, like Gecko) Version/10.1 Safari/603.1.30
Mozilla/5.0 (Windows NT 10.0; WOW64; Trident/7.0; rv:11.0) like Gecko
Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Ubuntu Chromium/56.0.2924.76 Chrome/56.0.2924.76 Safari/537.36
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:52.0) Gecko/20100101 Firefox/52.0
Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:51.0) Gecko/20100101 Firefox/51.0
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (X11; Linux x86_64; rv:52.0) Gecko/20100101 Firefox/52.0
Mozilla/5.0 (Windows NT 6.1; rv:52.0) Gecko/20100101 Firefox/52.0
Mozilla/5.0 (Macintosh; Intel Mac OS X 10.12; rv:51.0) Gecko/20100101 Firefox/51.0
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.110 Safari/537.36
Mozilla/5.0 (Windows NT 6.3; WOW64; rv:52.0) Gecko/20100101 Firefox/52.0
Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.133 Safari/537.36
Mozilla/5.0 (Macintosh; Intel Mac OS X 10.11; rv:52.0) Gecko/20100101 Firefox/52.0
Mozilla/5.0 (iPad; CPU OS 10_2_1 like Mac OS X) AppleWebKit/602.4.6 (KHTML, like Gecko) Version/10.0 Mobile/14D27 Safari/602.1
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.98 Safari/537.36
Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.98 Safari/537.36
Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.110 Safari/537.36
Mozilla/5.0 (Windows NT 10.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (Windows NT 6.1; Trident/7.0; rv:11.0) like Gecko
Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.98 Safari/537.36
Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36 OPR/43.0.2442.1144
Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.0; Trident/5.0;  Trident/5.0)
Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0;  Trident/5.0)
Mozilla/5.0 (iPhone; CPU iPhone OS 10_2_1 like Mac OS X) AppleWebKit/602.4.6 (KHTML, like Gecko) Version/10.0 Mobile/14D27 Safari/602.1
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/602.4.8 (KHTML, like Gecko) Version/10.0.3 Safari/602.4.8
Mozilla/5.0 (Windows NT 6.1; WOW64; rv:45.0) Gecko/20100101 Firefox/45.0
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_2) AppleWebKit/602.3.12 (KHTML, like Gecko) Version/10.0.2 Safari/602.3.12
Mozilla/5.0 (Windows NT 5.1; rv:52.0) Gecko/20100101 Firefox/52.0
Mozilla/5.0 (Windows NT 6.1; rv:51.0) Gecko/20100101 Firefox/51.0
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/601.7.7 (KHTML, like Gecko) Version/9.1.2 Safari/601.7.7
Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.110 Safari/537.36
Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.98 Safari/537.36
Mozilla/5.0 (X11; CrOS x86_64 9000.91.0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.110 Safari/537.36
Mozilla/5.0 (Macintosh; Intel Mac OS X 10.11; rv:51.0) Gecko/20100101 Firefox/51.0
Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.133 Safari/537.36
Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; rv:11.0) like Gecko
Mozilla/5.0 (X11; Linux x86_64; rv:51.0) Gecko/20100101 Firefox/51.0
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:52.0) Gecko/20100101 Firefox/52.0
Mozilla/5.0 (Windows NT 6.3; WOW64; rv:51.0) Gecko/20100101 Firefox/51.0
Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.110 Safari/537.36
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/603.1.30 (KHTML, like Gecko) Version/10.1 Safari/603.1.30
Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:50.0) Gecko/20100101 Firefox/50.0
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12) AppleWebKit/602.1.50 (KHTML, like Gecko) Version/10.0 Safari/602.1.50
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_1) AppleWebKit/602.2.14 (KHTML, like Gecko) Version/10.0.1 Safari/602.2.14
Mozilla/5.0 (Windows NT 6.1; rv:45.0) Gecko/20100101 Firefox/45.0
Mozilla/5.0 (X11; Fedora; Linux x86_64; rv:52.0) Gecko/20100101 Firefox/52.0
Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.76 Safari/537.36
Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:51.0) Gecko/20100101 Firefox/51.0
Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.133 Safari/537.36
Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.100 Safari/537.36
Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Ubuntu Chromium/53.0.2785.143 Chrome/53.0.2785.143 Safari/537.36
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.110 Safari/537.36
Mozilla/5.0 (Windows NT 10.0; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko
Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/57.0.2987.133 Safari/537.36'''

proxy_list = list() # 存储代理列表，防止过多访问代理网站


def get_response(url, use_proxy=True, num_retrials=4, timeout=5):
    """
    获取url的响应。可以设置代理和重试次数
    :param url: 要连接到网址
    :param proxy: 代理
    :param num_retrials: 重试次数
    :return: 响应
    """

    def random_ip():
        """
        get random ip from an ip list
        """

        def get_ips(url='http://haoip.cc/tiqu.htm', ips=proxy_list):
            """
            find the ip list shown in url
            """
            if len(proxy_list) != 0:  # 如果proxy_list已经有值，直接返回即可
                return ips
            try:
                response = requests.get(url, timeout=6)
                response.raise_for_status()
            except requests.HTTPError:
                print('代理池服务器出现问题')
            except requests.ReadTimeout:
                print('请检查代理地址')
            else:
                soup = BeautifulSoup(response.text, 'lxml')
                raw_ips = soup.select("div.col-xs-12")[0].text.replace(' ', '').split('\n')
                for raw_ip in raw_ips:
                    if raw_ip == '':
                        continue
                    ips.append(raw_ip)
                return ips

        ips = get_ips()
        random.seed()
        if ips is None:
            raise ValueError('代理异常')

        ip = random.choice(ips)
        return ip

    def ramdom_headers():
        """
        构造一个随机的Headers
        """
        random.seed()
        user_agent = random.choice(user_agents.split('\n'))
        headers = {'User-Agent': user_agent}
        return headers

    if not use_proxy:  # 如果不使用代理
        try:
            response = requests.get(url, headers=ramdom_headers(), timeout=timeout)
            response.raise_for_status()
        except:  # 如果上面的代码执行报错
            if num_retrials > 0:  # num_retrials是重试次数
                time.sleep(3)  # 延迟3秒
                print('获取网页出错，3S后将获取倒数第：', num_retrials, '次')
                return get_response(url, False, num_retrials - 1)  # 调用自身 并将次数减1
            else:
                print('开始使用代理')
                return get_response(url, True)  # 代理不为空的时候
        else:
            return response

    #else:  如果使用代理
    try:
        ip = random_ip()
    except ValueError as e:
        print(str(e))
    else:
        proxy = {'http': ip}  # 构造一个代理
        try:
            response = requests.get(url, headers=ramdom_headers(), proxies=proxy, timeout=timeout)  # 使用代理获取response
        except:
            if num_retrials > 0:
                time.sleep(10)
                print('将会更换代理，10S后将重新获取倒数第', num_retrials, '次')
                return get_response(url, True, num_retrials - 1)
            else:
                print('代理也不好使了！取消代理')
                return get_response(url, False)
        else:
            print('当前代理是：', proxy)
            return response


def get_categories(url):
    """
    获得页面的类目链接
    :param url: 页面链接
    :return: 链接网址
    """

    categories = set()  # set类型，防止重复链接

    r = get_response(url)

    soup = BeautifulSoup(r.text, 'lxml')
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
        r = get_response(url)
        soup = BeautifulSoup(r.text, 'lxml')
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

        time.sleep(1.5)

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

    for cat in cats:
        get_products_of_all_pages(cat)


start()
```

这个爬虫很可能很快就不能爬取58同城内容了，但是没有关系，基本思想都在这里了。
