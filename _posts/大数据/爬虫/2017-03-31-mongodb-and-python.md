---
layout: post
title:  "6. MongoDB与Python"
categories: 爬虫
tags: MongoDB PyMongo
author: Geng
---

* content
{:toc}


我们已经熟悉了MongoDB的基本知识，下一步就是如何让它与Python关联起来了。

首先，为了方便入门，选择一个合适的IDE还是很重要的，因为**IntelliJ**全家桶包括范围很广，快捷键统一，查看源代码方便，所以我还是选择Pycharm了。

然后，为了更好地与数据库交互，可以安装一个“Mongo Plugin”。

最后，使用**PyMongo**就可以让Python和MongoDB交互了。






## PyCharm设置
我使用**PyCharm**来做爬虫IDE。首先，安装“Mongo Plugin”到**PyCharm**。重启IDE后即可开始设置并使用了。（如果不会安装插件，请看[这里](http://blog.csdn.net/qkxh320/article/details/18008111)，其实都是我临时搜索出来的链接😂）

重启后，可以发现右边侧边栏看到`Mongo Explorer`菜单。打开它，点击`设置`按钮。

![](https://github.com/dboissier/mongo4idea/raw/master/doc/mongo4idea_noConfiguration.png)

输入你的*mongo*路径。如果不知道，在Mac系统下，可以执行下面命令：

```bash
$ which mongo
/usr/local/bin/mongo
```

然后把输出的路径，这里我的是"/usr/local/bin/mongo"，输入到`Path to Mongo Shell`中。点击`Test`检查是否正确。没有问题的话，点击左下角➕，弹出框中`Label`位置输入这个配置的名称，这里我直接输入*localhost*。

![](https://github.com/dboissier/mongo4idea/raw/master/doc/mongo4idea_pluginConfiguration.png)

![](https://github.com/dboissier/mongo4idea/raw/master/doc/mongo4idea_serverConfigurationGeneralTab.png)

然后我们准备在PyCharm连接MongoDB。首先在终端中输入`mongod`启动数据库，然后点击下图插销按钮连接。

![]({{ site.url }}/assets/images/posts/spider/2017-03-31-mongodb-and-python/2.png)

连接成功后，我们会看到类似下图的界面：

![]({{ site.url }}/assets/images/posts/spider/2017-03-31-mongodb-and-python/3.png)

这里的显示了三个数据库，分别是“admin”， “local”和“test”。点击一个想要操作的数据库，然后点击上面的`Mongo Sehll`按钮，可以打开一个*Mongo Sehll*

更多使用方法，可以参考[官方文档](https://github.com/dboissier/mongo4idea)

## 使用PyMongo
使用**PyMongo**让Python和MongoDB交互，也就是Python代码操作MongoDB。下面大多数内容来自[官方文档](http://api.mongodb.com/python/current/tutorial.html?_ga=1.106763651.123499832.1490441450#tutorial)。

首先在终端中输入`mongod`启动数据库，再进行下面操作

导入PyMongo：
```python
import pymongo
```

### 连接数据库
回想在终端，我们是不是先用下面命令连接数据库：
```bash
$ mongo
>
```

这里，我们也需要连接数据库才能使用：
```python
import pymongo
client = pymongo.MongoClient()
```

上面代码连接数据库，而且使用默认的主机和端口。它等同于
```python
client = pymongo.MongoClient('localhost', 27017)
```

或者：
```python
client = pymongo.MongoClient('mongodb://localhost:27017/')
```

### 连接到一个集合
为了操作数据，我们首先要连接到一个集合才可以。

一个MongoDB实例不只有一个数据库，所以为了连接到一个集合，首先要说明连接到哪个数据库。

连接或者新建并连接一个数据库:
```python
db = client.test_database
```

或者：
```python
db = client['test-database']
```

连接上数据库后，我们就可以开始操作集合了。
连接或者新建并连接一个结合：
```python
collection = db.test_collection
```

或者：
```python
collection = db['test-collection']
```

> 上面新建并连接的操作都是懒的。只有在第一个文档写入的时候，才会真正的新建。

### 文档操作
前面介绍过，文档是以**BSON**数据格式存储的，它类似于JSON，命名其实就是“Binary JSON"。PyMongo使用字典数据类型来描述文档。例如下面的字典可以用来表示一个博客文章。
```python
>>> import datetime
>>> post = {"author": "Mike",
...         "text": "My first blog post!",
...         "tags": ["mongodb", "python", "pymongo"],
...         "date": datetime.datetime.utcnow()}
```

#### 增
如果要添加一个文档，可以使用`insert_one()`：
```python
>>> posts = db.posts
>>> post_id = posts.insert_one(post).inserted_id
>>> post_id
ObjectId('...')
```

文档添加的同时，如果没有设置`_id`，自动会有`_id`创建。`_id`必须是唯一存在于一个集合的。

`insert_one()`会返回一个`InsertOneResult`实例。然后我又使用`inserted_id`返回了其`_id`。

确认已经有我们创建的集合，可以列出数据库中所有集合：
```python
>>> db.collection_names(include_system_collections=False)
```

如果要添加多个文档，可以使用`insert_many()`：
```python
>>> new_posts = [{"author": "Mike",
...               "text": "Another post!",
...               "tags": ["bulk", "insert"],
...               "date": datetime.datetime(2009, 11, 12, 11, 14)},
...              {"author": "Eliot",
...               "title": "MongoDB is fun",
...               "text": "and pretty easy too!",
...               "date": datetime.datetime(2009, 11, 10, 10, 45)}]
>>> result = posts.insert_many(new_posts)
>>> result.inserted_ids
[ObjectId('...'), ObjectId('...')]
```

#### 查
最基本的就是查询一个符合条件的文档，使用`find_one()`。如果有匹配的，那么只会返回第一个匹配值。如果没有，返回`None`。

如果仅仅想要返回第一个文档：
```python
>>> import pprint
>>> pprint.pprint(posts.find_one())
{u'_id': ObjectId('...'),
 u'author': u'Mike',
 u'date': datetime.datetime(...),
 u'tags': [u'mongodb', u'python', u'pymongo'],
 u'text': u'My first blog post!'}
```

如果是返回匹配某个条件的文档：
```python
>>> pprint.pprint(posts.find_one({"author": "Mike"}))
{u'_id': ObjectId('...'),
 u'author': u'Mike',
 u'date': datetime.datetime(...),
 u'tags': [u'mongodb', u'python', u'pymongo'],
 u'text': u'My first blog post!'}
 ```
 
类似语法，就可以如下根据`_id`来查询了
```python
>>> post_id
ObjectId(...)
>>> pprint.pprint(posts.find_one({"_id": post_id}))
{u'_id': ObjectId('...'),
 u'author': u'Mike',
 u'date': datetime.datetime(...),
 u'tags': [u'mongodb', u'python', u'pymongo'],
 u'text': u'My first blog post!'}
 ```

但是需要注意，`post_id`类型是`ObjectId`，而不是字符串。所以如果得到一个字符串类型的`_id`，需要进行类型转换：
```python
from bson.objectid import ObjectId

# The web framework gets post_id from the URL and passes it as a string
def get(post_id):
    # Convert from string to ObjectId:
    document = client.db.collection.find_one({'_id': ObjectId(post_id)})
```

> Unicode字符串参考：[A Note On Unicode Strings](http://api.mongodb.com/python/current/tutorial.html?_ga=1.106763651.123499832.1490441450#a-note-on-unicode-strings)和[Unicode HOWTO](https://docs.python.org/3/howto/unicode.html)。比如解释了u’Mike’而不是‘Mike’的原因

怎么查询多个文档呢？可以使用`find()`，它返回一个`Cursor`实例，这个实例可以遍历所有匹配文档.

遍历所有文档：
```python
>>> for post in posts.find():
...   pprint.pprint(post)
...
{u'_id': ObjectId('...'),
 u'author': u'Mike',
 u'date': datetime.datetime(...),
 u'tags': [u'mongodb', u'python', u'pymongo'],
 u'text': u'My first blog post!'}
{u'_id': ObjectId('...'),
 u'author': u'Mike',
 u'date': datetime.datetime(...),
 u'tags': [u'bulk', u'insert'],
 u'text': u'Another post!'}
{u'_id': ObjectId('...'),
 u'author': u'Eliot',
 u'date': datetime.datetime(...),
 u'text': u'and pretty easy too!',
 u'title': u'MongoDB is fun'}
 ```

遍历匹配文档：
```python
>>> for post in posts.find({"author": "Mike"}):
...   pprint.pprint(post)
...
{u'_id': ObjectId('...'),
 u'author': u'Mike',
 u'date': datetime.datetime(...),
 u'tags': [u'mongodb', u'python', u'pymongo'],
 u'text': u'My first blog post!'}
{u'_id': ObjectId('...'),
 u'author': u'Mike',
 u'date': datetime.datetime(...),
 u'tags': [u'bulk', u'insert'],
 u'text': u'Another post!'}
 ```
 
#### 计数
使用`count()`即可
```python
>>> posts.count()
3
>>> posts.find({"author": "Mike"}).count()
2
```

#### 索引
添加索引可以提高特定查询速度还能提供更多其他功能。数据库自动创建的`_id`本身就是索引。

首先，我们可以建立一个索引：
```python
>>> result = db.profiles.create_index([('user_id', pymongo.ASCENDING)],
...                                   unique=True)
>>> sorted(list(db.profiles.index_information()))
[u'_id_', u'user_id_1']
```

我们可以这样添加一些文档：
```python
>>> user_profiles = [
...     {'user_id': 211, 'name': 'Luke'},
...     {'user_id': 212, 'name': 'Ziltoid'}]
>>> result = db.profiles.insert_many(user_profiles)
```

因为索引具有唯一性，所以是不能重复的，例如下面报错：
```python
>>> new_profile = {'user_id': 213, 'name': 'Drew'}
>>> duplicate_profile = {'user_id': 212, 'name': 'Tommy'}
>>> result = db.profiles.insert_one(new_profile)  # This is fine.
>>> result = db.profiles.insert_one(duplicate_profile)
Traceback (most recent call last):
DuplicateKeyError: E11000 duplicate key error index: test_database.profiles.$user_id_1 dup key: { : 212 }
```


#### 改和删
可以发现，PyMongo的语法与Mongo shell没什么太大区别。

改可以写为：
```python
db.collection.update_one(
        {查询条件},
        {更新}
    )
```

例如：
```python
db.Employees.update_one(
        {"id": criteria},
        {
        "$set": {
            "name":name,
            "age":age,
            "country":country
        }
        }
    )
```

删除的话：
```python
db.collection.delete_many({条件})
```

## 实操
```python
import pymongo

client = pymongo.MongoClient()
db = client.store
products = db.products


path = './products.txt'

with open(path, 'r') as file:
    for line in file:
        line = line.rstrip()
        data = {
            "content": line,
            "length": len(line)
        }
        products.insert_one(data)

for product in products.find():
    print(product)
```

上面代码中，使用了前面学的知识和基本Python读取文件的方法，最后输出打印了数据库中的内容。
