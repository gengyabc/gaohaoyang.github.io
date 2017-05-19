---
layout: post
title:  "10. 使用Appium测试iOS应用"
categories: 持续集成
tags:  Appium python
author: Geng
---

* content
{:toc}


我们已经知道了如何安装，启动Appium，下一步就是开始真正测试了。最初本来是准备使用**Ruby**的，因为Appium官网的例子是以**Ruby**为主，**Ruby**写起来也挺舒服。但是国内测试圈还是**Python**为主，所以这里我就开始使用**Python**来写测试用例了。






## 排雷
本来一切顺利，但是在运行测试代码的过程中，我发现会出现莫名其妙的bug，根据github issue，发现是**selenium**的问题，我们可以首先卸载高版本的**selenium**，再安装低版本的**selenium**：

```bash
pip uninstall selenium
pip install -U selenium==3.3.0
```

这个坑填平后，我们可以开始测试了。

## python-client介绍
我们在[7. 测试前准备 -- 更新]({{ post_url 2017-02-28-Prepare-to-test.md }})中已经介绍过**python-client**的安装，这里不再重复。

这里只是简单介绍一下，大概有个了解即可，否则这部分成了测试内容了。

### 使用
**Appium Python Client**与**Selenium 3.0**兼容，主要使用方法与**Selenium 2 (WebDriver)**是一样的。我们测试的时候，使用的是Selenium webdriver的超集，即appium中的webdriver：

```python
from appium import webdriver
```

设置[UnitTest](https://docs.python.org/2/library/unittest.html)环境：

```python
# Android environment
import unittest
from appium import webdriver

desired_caps = {}
desired_caps['platformName'] = 'Android'
desired_caps['platformVersion'] = '4.2'
desired_caps['deviceName'] = 'Android Emulator'
desired_caps['app'] = PATH('../../../apps/selendroid-test-app.apk')

self.driver = webdriver.Remote('http://localhost:4723/wd/hub', desired_caps)
```

```python
# iOS environment
import unittest
from appium import webdriver

desired_caps = {}
desired_caps['platformName'] = 'iOS'
desired_caps['platformVersion'] = '7.1'
desired_caps['deviceName'] = 'iPhone Simulator'
desired_caps['app'] = PATH('../../apps/UICatalog.app.zip')

self.driver = webdriver.Remote('http://localhost:4723/wd/hub', desired_caps)
```

### 定位元素
Appium有多种定位元素的方法，比如XPath，class，id等。根据实际使用和官方推荐，建议大家使用id来定位元素，在Appium中，就是**Accessibility ID**。对于iOS来说，是**accessibility identifier**，或者没有设置**accessibility identifier**的话，是显示的名称；对于Android来说，是**content-description**：

```python
self.driver.find_element_by_accessibility_id('Animation')
```

### 操作
Appium提供了Selenium 3.0的触摸和多点操作。

## 一个简单的测试
首先，我们需要一个例子，可以到苹果官网下载这个[小例子](https://developer.apple.com/library/content/samplecode/TableSearch_UISearchController/Introduction/Intro.html)。

 下载下来后，运行应用并找到“.app”文件路径，详细代码如下：

```python

"""
简单iOS测试，初次尝试使用Appium.
"""
import unittest
import os
from appium import webdriver
from time import sleep

class TableSearchTests(unittest.TestCase):

    def setUp(self):
        # Set up appium
        app = os.path.abspath('/Users/www.wanwu.tech/Desktop/Search.swift.app')
        self.driver = webdriver.Remote(
            command_executor='http://127.0.0.1:4723/wd/hub',
            desired_capabilities={
                'app': app,
                'platformName': 'iOS',
                'platformVersion': '10.2',
                'deviceName': 'iPhone Simulator',
                'bundleId':'com.example.apple-samplecode.Search-swift'
            })


    def test_search_field(self):
        # 定位
        search_element = self.driver.find_element_by_accessibility_id("Search")
        # 输入
        search_element.send_keys("iPad")
        # 等待
        sleep(2)

        ipad = self.driver.find_element_by_accessibility_id("iPad")
        # 确定真的有
        self.assertEqual('iPad', ipad.get_attribute('name'))


    def tearDown(self):
        self.driver.quit()


if __name__ == '__main__':
    suite = unittest.TestLoader().loadTestsFromTestCase(TableSearchTest)
    unittest.TextTestRunner(verbosity=2).run(suite)

```

上面代码比较简单，仔细看看应该没有什么难度，这里只是简单介绍下如何结合**Appium Inspector**来使用。

下图可见，点击`Search`搜索框，在右侧可以标注红框位置，可以发现提示，根据这个提示，使用如下代码：

```python
# 定位
search_element = self.driver.find_element_by_accessibility_id("Search")
```

然后在这个输入框中，我们可以输入比如“ipad”，进行搜索。在Appium中，可以点击下图红框上面的`Send Keys`，输入“ipad”进行搜索。因为Appium比较慢，所以这里等待了两秒钟：

```python
search_element.send_keys("iPad")
# 等待
sleep(2)
```

![]({{ site.url }}/assets/images/posts/CI/2017-05-15-ios-testing-appium-python/find_element_by_id.png)

进入下一个界面后，还是通过类似的方法，定位了一个元素，然后使用Python单元测试Assert语句，判断时候正确。这里使用了Appium中`et_attribute()`方法，具体有哪些属性，可以参考下图红框的部分：

```python
ipad = self.driver.find_element_by_accessibility_id("iPad")
# 确定真的有
self.assertEqual('iPad', ipad.get_attribute('name'))
```

![]({{ site.url }}/assets/images/posts/CI/2017-05-15-ios-testing-appium-python/attributes.png)

其他部分就是测试启动结束和appium一些固定操作，部分已经介绍过，其他基本都是固定写法，不再过多介绍。

## 结语
这里简单介绍了Appium的使用，因为这部分内容主要是持续集成的内容，所以我这里不准备深入Appium的内容，下半年我会在**移动应用测试**中详细介绍Appium使用，欢迎关注。
