---
layout: post
title:  "4. MongoDB教程 -- 入门"
categories: 爬虫
tags: MongoDB 
author: Geng
---

* content
{:toc}

学爬虫，怎么又来了MongoDB？当然是为了存储数据。

因为爬取的数据，得有一个地方存放，而大数据的存放，使用NoSQL比较好。其中，MongoDB就是一种很常用的NoSQL。MongoDB是一种开源的数据库，为了方便开发和扩展而设计。

这不是一个面面俱到的教程，以后如果有需要，我可能会做一个更深入的教程。






## 介绍
MongoDB，多么奇怪的名字，芒果数据库吗？呵呵。

Mongo来源于humongous，意思是**巨大无比的**。那么名正言顺，它适合于存放巨大数据。

那么它与SQL有什么不同呢？我们来看下下面这个图：

![]({{ site.url }}/assets/images/posts/spider/2017-03-28-MongoDB-intro/1.png))

可以看到，相对于SQL的关系型数据库，MongoDB是基于**文档**（docunment）的。**文档**对应**row**，**集合**（collection）对应**table**。关系型数据库将数据存在table中的row里。而MongoDB将数据存在集合中的文档里。

MongoDB一条记录就是一个文档，类似于字典数据类型，或者JSON对象：

```js
{
   "_id" : ObjectId("54c955492b7c8eb21818bd09"),
   "address" : {
      "street" : "2 Avenue",
      "zipcode" : "10075",
      "building" : "1480",
      "coord" : [ -73.9557413, 40.7720266 ]
   },
   "borough" : "Manhattan",
   "cuisine" : "Italian",
   "grades" : [
      {
         "date" : ISODate("2014-10-01T00:00:00Z"),
         "grade" : "A",
         "score" : 11
      },
      {
         "date" : ISODate("2014-01-16T00:00:00Z"),
         "grade" : "B",
         "score" : 17
      }
   ],
   "name" : "Vella",
   "restaurant_id" : "41704620"
}
```


集合是无模式的，集合中的文档可以是各式各样的。

## Mac安装及启动
安装很简单，使用Homebrew就可以了：

```bash
brew install mongodb
```

然后可以查看下版本：
```bash
mongo -version
```

下一步建立一个数据库文件夹。MongoDB将数据存在"/data/db"，你需要手动建立然后分配权限。

```bash
$ sudo mkdir -p /data/db
$ whoami
me
$ sudo chown me /data/db
```

然后就可以启动MongoDB了：

```bash
$ mongod
```

如果有权限问题，那么使用：`$ sudo mongod`

**mongod**是MongoDB主要的守护进程，它处理数据请求，控制数据读取，还会处理后台管理操作。当看到`waiting for connections on port 27017`字样，说明启动成功了。

打开另一个终端，输入`mongo`可以连接数据库进行操作。

更详细文档，见[官方文档](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)。

## 基本操作
我们先在终端中了解下MongoDB的基本操作：

根据上文内容，确定MongoDB，已经启动，然后连接数据库：
```bash
$ mongo
>
```

在**大于号**后面，我们就可以开始和数据库交互了：

```js
> var person = {
... "name": "xiao ming",
... "age": "4"
... }
```

上面代码，我们使用JavaScript创建了变量的语法，创建了一个**文档**，然后，我们可以输入其名字，显示其内容：

```js
> person
{ "name" : "xiao ming", "age" : "4" }
> 
```

输出的内容就是我们要的**文档**了。

我们现在用的是什么数据库呢：
```bash
> db
test
```

如果想使用某个数据库，可以：
```bash
$ use mytest
```

这个命令说明你要用**mytest**这个数据库，如果没有这个数据库，那么会先新建一个叫做**mytest**的数据库，然后再使用它。

可见，可以看到我们使用的是**test**数据库。

下面再看看如何找到我们想要的操作：
```bash
> help
	db.help()                    help on db methods
	db.mycoll.help()             help on collection methods
	sh.help()                    sharding helpers
	rs.help()                    replica set helpers
	help admin                   administrative help
	help connect                 connecting to a db help
	help keys                    key shortcuts
	help misc                    misc things to know
	help mr                      mapreduce

	show dbs                     show database names
	show collections             show collections in current database
	show users                   show users in current database
	show profile                 show most recent system.profile entries with time >= 1ms
	show logs                    show the accessible logger names
	show log [name]              prints out the last segment of log in memory, 'global' is default
	use <db_name>                set current database
	db.foo.find()                list objects in collection foo
	db.foo.find( { a : 1 } )     list objects in foo where a == 1
	it                           result of the last line evaluated; use to further iterate
	DBQuery.shellBatchSize = x   set default number of items to display on shell
	exit                         quit the mongo shell
```

假设我们想要查看现在有什么数据库，根据上面帮助文档可见：
```bash
> show dbs
```

### 插入
**文档**需要存放在**集合**中，我们可以使用**集合**的**insert()** 方法将一个文档放入**集合**：
```js
> db.tutorial.insert({"name":"mongodb"})
WriteResult({ "nInserted" : 1 })
```

这里，你将`{"name":"mongodb"}`放在现在使用的数据库的叫做**tutorial**的**集合**中。如果这个集合不存在，那么会先创建。如果成功插入一条数据，那么会返回一个提示`WriteResult({ "nInserted" : 1 })`，说明成功写入了一条数据。自己试着写入更多数据。

### 查找
使用`find()`方法，可以查看本**集合**的所有**文档**
```js
> db.potions.find()
{ "_id" : ObjectId("58d8842c21b9f8293f377915"), "name" : "mongodb" }
```

因为每一个**文档**都需要有一个唯一的id，如果我们自己没指定，那么MongoDB会为我们建一个`ObjectId`类型的id。

也可以根据键名来查找，可以找到所有符合条件的文档：
```bash
> db.tutorial.find({"name": "mongo"})
{ "_id" : ObjectId("58d8844a21b9f8293f377916"), "name" : "mongo" }
```

那么文档中还可以存储什么呢？文档是以**BSON**数据格式存储的，它类似于JSON，命名其实就是“Binary JSON"，但是还提供了更多的数据类型，比如对象`ObjectID`，`ISODate`。

### 使用插入和查找
我们来做一个小练习，实际使用一下上面的知识。
我想要描述一个人的信息，首先假设是`people`集合，需要姓名和身高信息，我们可以如下设计数据：

```js
{"name": "xiaoming", "height": 167}
```

我认为还需要出生年月，然后将它插入集合：
```js
> db.people.insert({"name": "xiaohong", "height": 183, "birthday": new Date(2000, 8, 30)})
WriteResult({ "nInserted" : 1 })
> db.people.find()
{ "_id" : ObjectId("58d8ea10a45824bc146a0b8a"), "name" : "xiaohong", "height" : 183, "birthday" : ISODate("2000-09-29T16:00:00Z") }
```

注意日期不是**2000-08-30**，而是**2000-09-29**，这是因为JavaScript记录日期从0开始。

那么假设我还想记录一个人的朋友姓名呢，可以这样设计数据结构：

```js
{
  "name": "xiaohong",
  "height": 183,
  "birthday": new Date(2000, 8, 30),
  "friends": ["xiaoming", "xiaowang", "xiaoli"]
}
```

上面数据显示，我们可以存储对象，也可以存储一个数组

如果再记录一个人的技术能力分值呢？
```js
{
  "name": "xiaohong",
  "height": 183,
  "birthday": new Date(2000, 8, 30),
  "friends": ["xiaoming", "xiaowang", "xiaoli"],
  "skill": {"run": 9, "jump": 7}
}
```

注意，上面其实其实嵌套了一个文档，而且这个文档不需要**id**。

如果满意了，我们可以将这个数据结构加入我们的集合：
```js
> db.people.insert({
  "name": "xiaohong",
  "height": 183,
  "birthday": new Date(2000, 8, 30),
  "friends": ["xiaoming", "xiaowang", "xiaoli"],
  "skill": {"run": 9, "jump": 7}
})
```

你可以试着再添加一下文档进去，这里我就不再浪费时间了。下面我们试试查找数据。
```js
> db.people.find({"name": "xiaohong"})
{ "_id" : ObjectId("58d8e829d6a297f93509a04d"), "name" : "xiaohong", "height" : 183 }
{ "_id" : ObjectId("58d8ed13a45824bc146a0b8b"), "name" : "xiaohong", "height" : 183, "birthday" : ISODate("2000-09-29T16:00:00Z"), "friends" : [ "xiaoming", "xiaowang", "xiaoli" ], "skill" : { "run" : 9, "jump" : 7 } }
```

那我想要找技能点中的跑，值为9，怎么办？
```js
> db.people.find({"skill.run": 9})
{ "_id" : ObjectId("58d8ed13a45824bc146a0b8b"), "name" : "xiaohong", "height" : 183, "birthday" : ISODate("2000-09-29T16:00:00Z"), "friends" : [ "xiaoming", "xiaowang", "xiaoli" ], "skill" : { "run" : 9, "jump" : 7 } }
```

可见，我们可以使用**点**语法。

不过，上面查询语法输出有点丑，我们可以使用`pretty()`美化一下：
```js
> db.people.find({"skill.run": 9}).pretty()
{
	"_id" : ObjectId("58d8ed13a45824bc146a0b8b"),
	"name" : "xiaohong",
	"height" : 183,
	"birthday" : ISODate("2000-09-29T16:00:00Z"),
	"friends" : [
		"xiaoming",
		"xiaowang",
		"xiaoli"
	],
	"skill" : {
		"run" : 9,
		"jump" : 7
	}
}
```

另外注意，MongoDB是无模式的，那么你认为不正确的输入，它也是认可的：
```js
> db.people.insert({
  "name": 9,
  "height": “183cm”,
  "birthday": new Date(2000, 8, 30),
  "friends": ["xiaoming", 3, "xiaoli"],
  "skill": {"run": “nine”, "jump": 7}
})
```

不符合你认为的数据类型？MongoDB可不管这些。只要满足下面三个条件就可以：
1. id唯一
2. 语法正确
3. 文档小于16mb


## 大小判断
我们一直都在用相等，现在看看其他操作，详细的查询操作可以见下表[^1]

| 操作  | 语法 | 例子 |
| ------------- | ------------- | ------------- |
| 相等  | {\<key\>:\<value\>} | db.mycol.find({"by":"tutorials point"}).pretty() |
| 小于  | {\<key\>:{\$lt:\<value\>}} | db.mycol.find({"likes":{\$lt:50}}).pretty() |

其他小于等于，大于，大于等于，不相等，对应位置依次改为：`$lte`，`$gt`，`$gte`，`$ne`即可。
