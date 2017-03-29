---
layout: post
title:  "5. MongoDB教程 -- 更多内容"
categories: 爬虫
tags: MongoDB 
author: Geng
---

* content
{:toc}

数据库应该有基本的增删改查功能，上一章我们介绍了增和查。这一章，我们重点说下怎么删改。然后再介绍比如过滤，索引等知识。





## 删除
假设我们通过`find()`命令，发现我们有如下文档：
```js
> db.people.find()
{ "_id" : ObjectId("58d906c3a45824bc146a0b8d"), "name" : "xiaohong", "height" : 167, "birthday" : ISODate("2000-09-29T16:00:00Z"), "friends" : [ "xiaoming", "xiaowang", "xiaoli" ], "skill" : { "run" : 9, "jump" : 7 } }
{ "_id" : ObjectId("58d906d2a45824bc146a0b8e"), "name" : "xiaoming", "height" : 177, "birthday" : ISODate("2000-09-29T16:00:00Z"), "friends" : [ "xiaoming", "xiaowang", "xiaoli" ], "skill" : { "run" : 9, "jump" : 7 } }
{ "_id" : ObjectId("58d906d7a45824bc146a0b8f"), "name" : "xiaoming", "height" : 177, "birthday" : ISODate("2000-09-29T16:00:00Z"), "friends" : [ "xiaoming", "xiaowang", "xiaoli" ], "skill" : { "run" : 9, "jump" : 0 } }
{ "_id" : ObjectId("58d908f7a45824bc146a0b90"), "name" : "xiaohong", "height" : 177, "birthday" : ISODate("2002-10-29T16:00:00Z"), "friends" : [ "xiaoming", "xiaowang", "xiaoli" ], "skill" : { "run" : 9, "jump" : 7 } }
```

我们想要删除第一个文档：
```js
> db.people.remove({"height" : 167})
WriteResult({ "nRemoved" : 1 })
```
再次运行`find()`命令，可以发现这个文档被删除了。**可见，使用集合的`remove()`命令，可以删除符合条件的文档**，其中`remove()`中的语句，就是查询语句，符合条件的文档就是说符合这个查询语句的所有文档。如果有多个文档都符合条件，那么所有符合条件的文档都会删除。

> 危险：如果直接`db.people.remove({})`，将会删除所有文档。

## 修改

### 修改属性
假设在录入的时候，我有信息填错了，怎么办呢，我们可以使用`update()`：
```js
> db.people.update({"height": 177},{"$set":{"height": 187}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
```

这里的语法如下：
```js
> db.集合.update(查询条件,更新数据)
```
其中使用`$set`可以说明是更新这个文档的这个属性，如果写成`db.people.update({"height": 177},{"height": 187})`，那么符合这个条件的文档会被修改为只有`{"height": 187}`，而不是仅仅修改这个属性。

另外需要注意，这句话只能修改**第一个符合条件的文档**。如果想要修改全部符合条件的文档，需要增加第三个参数`{"multi": true}`：

```js
> db.people.update({"height": 177},{"$set":{"height": 187}}, {"multi": true})
```

### 计数
假设我们文档中有一个属性记录个人记录被浏览次数，每次浏览都会加一，我们可以如下操作：
```js
> db.people.update({"name": "xiaohong"},{"$inc":{"count": 1}})
```
现在没有`count`这个属性，那么第一次使用会被赋值1。在调用同样语句，看看效果。是不是`count`变2了。

### 改增
观察下面代码：
```js
> db.people.update({"name": "xiaoli"},{"$inc":{"count": 1}})
WriteResult({ "nMatched" : 0, "nUpserted" : 0, "nModified" : 0 })
```
我们的集合中并没有名字为*xiaoli*的文档，那么这句就什么都不做了。

但是如果这样写：
```js
> db.people.update({"name": "xiaoli"},{"$inc":{"count": 1}}, {"upsert": true})
WriteResult({
	"nMatched" : 0,
	"nUpserted" : 1,
	"nModified" : 0,
	"_id" : ObjectId("58d91f789ffb01193cc69467")
})
```
我们会添加一个新的文档。

我们来看下到底发生了什么。
首先，`upsert`是“updates an existing document or inserts a new one”，简称：“update or insert”。也就是说，如果有，那么更新，方法同没有`{"upsert": true}`的语句。如果没有，那么添加。

其次，`"nMatched" : 0, "nUpserted" : 0, "nModified"` 是什么？观察名字，其实就是匹配的数量，改增的数量，修改的数量。

### 删除属性
我们刚开始都设置了一个生日属性，但是后来发现好像不需要用到，那么，我们怎么可以删除这个属性呢？

```js
> db.people.update({},{"$unset": {"birthday":""}}, {"multi": true})
```

首先，`{}`说明我们查询所有文档。其次`$unset`说明我们要删除某个属性，后面跟属性名，至于属性名的值是什么，没有什么关系，因为我们要把整个属性删除。最后`{"multi": true}`说明删除所有的，否则只会匹配第一个。

### 修改属性名
如果我们发现属性名写错了怎么办。我们的技能属性名，应该用复数，但是我在建立的时候用的是单数，如何修改呢：
```js
> db.people.update({},{"$rename": {"skill":"skills"}}, {"multi": true})
```

可见，使用`$rename`即可。

### 修改数组属性
观察我们的数据，会发现朋友属性是一个数组：`"friends" : [ "xiaoming", "xiaowang", "xiaoli" ]`。如果想要将数组里面某个值做改变呢？我们之前学的方法，都是整体修改属性值，对于数组，可以用下面方法：
```js
db.people.update({"name": "xiaoming"},{"$set": {"friends.1": "change"}} )
```

可以发现，我们使用了数组索引，但不是不同于一般的`friends[1]`，我们使用点语法：`friends.1`，可以看到，第二个元素的值会发生变化。

问题又来了，如果我的数组里面有一个值，比如“xiaowang”，想全部改成其他值，比如”haha“，怎么办？我们看下下面代码：
```js
> db.people.update({"friends": "xiaowang"},{"$set": {"friends.$": "111"}}, {"multi": true} )
```

`update()`中的第一个大括号是查询条件，第二个大括号中"friends.$"中的“$"是匹配项的占位符，最后的参数我们已经知道了，它使得一次可以改变多个文档。


### 修改嵌套文档
我们上一章见过这个查询：
查询技能点中的跑的值为9，使用了**点**语法
```js
> db.people.find({"skills.run": 9})
{ "_id" : ObjectId("58d8ed13a45824bc146a0b8b"), "name" : "xiaohong", "height" : 183, "birthday" : ISODate("2000-09-29T16:00:00Z"), "friends" : [ "xiaoming", "xiaowang", "xiaoli" ], "skill" : { "run" : 9, "jump" : 7 } }
```

那么想一个问题，怎么修改嵌套文档的某个值？是不是很简单,直接用点即可：
```js
> db.people.update({"name": "xiaoming"}, {"$set": {"skill.run": 77}})
```

## 投影
观察下面代码：
```js
> db.people.find({"name": "xiaoming"}, {"skills.jump": true})
{ "_id" : ObjectId("58d906d2a45824bc146a0b8e"), "skills" : { "jump" : 7 } }
{ "_id" : ObjectId("58d906d7a45824bc146a0b8f"), "skills" : { "jump" : 0 } }
```
这段代码中，第一部分是查询条件，第二部分是返回条件。符合查询条件的文档，只会返回符合返回条件的属性（设定为true的那些属性返回，其他属性默认为false）和id。这个其实就是说，只显示我要求显示的。

当然，如果你不想要输出id，也很简单：
```js
> db.people.find({"name": "xiaoming"}, {"skills.jump": true,"_id": false})
{ "skills" : { "jump" : 7 } }
{ "skills" : { "jump" : 0 } }
```
只要`"_id": false`即可。

但是：
```js
> db.people.find({"name": "xiaoming"}, {"skills.jump": false})
{ "_id" : ObjectId("58d906d2a45824bc146a0b8e"), "name" : "xiaoming", "height" : 187, "count" : 2, "skills" : { "run" : 9 }, "friends" : [ "xiaoming", "change", "xiaoli" ], "skill" : { "run" : "go" } }
{ "_id" : ObjectId("58d906d7a45824bc146a0b8f"), "name" : "xiaoming", "height" : 187, "skills" : { "run" : 9 }, "friends" : [ "xiaoming", "111", "xiaoli" ] }
```
注意，这个时候，设定为false的那些属性不返回，其他属性默认为true。这个就是说，不显示不要求显示的。

我们上面看到的`find()`第二个参数，就是**投影**

## 游标
每当我们查询文档的时候，`find()`方法都会返回一个对象，这个对象是一个**游标对象**。为什么要用游标呢？可以[参考这个回答](http://stackoverflow.com/questions/3861558/what-are-the-benefits-of-using-database-cursor)。简单来说，就是这样做更有效率。其实，我们可以从游标的名字来看，就是读取数值的那个游标，或者是读书的时候眼睛在看的区域，手指的区域。游标做到指哪读哪，不指哪不读哪。

### 数量
如果查询返回的文档很多，那么每次只会显示20个文档，然后会提示输入`it`以查看更多：
```js
> db.largeone.find()
.
.
.
Type "it" for more
```

游标对象可以返回符合条件的文档数目：
```js
> db.largeone.find().count()
```
因为`find()`方法返回游标对象，所以我们可以使用游标的`count()`方法。

### 排序
排序可以使用`sort()`方法：
```js
> db.largeone.find().sort({"price": 1})
```

`{"price": 1}`说明按照*price*的升序排序。如果传入-*1*，那么就是降序排序。

### 分页
分页就是每次只展示一部分，还必须能够展示下一页和后面的页。我们首先应该确定每页展示多少文档，可以使用`limit(数量)`。然后要确定展示的这页从哪里开始，也就是前面掠过了多少文档，可以使用`skip(数量)`。

那么，如果第一页，不需要掠过任何文档：
```js
> db.largeone.find().limit(5)
```
说明显示前5个文档。

如果第三页，那需要掠过 3 * 5 = 15 个文档：
```js
> db.largeone.find().skip(15).limit(5)
```

## 数据索引简介
我们前几章使用了内嵌的文档来存储复杂信息，但是考虑存储人员信息的数据库，我们想要添加某人朋友信息，那么其朋友也要有姓名，年龄等信息。但是我们要存储所有人信息，那么我们也会把这个朋友也作为文档存储。这样，这位朋友不仅独立为一个文档存储，也最为内嵌文档嵌入在某人的信息中。同样的信息，多个位置存储，显然不是一个好的方案。鉴于此，我们使用数据索引。

我们来[实际操作](https://docs.mongodb.com/manual/reference/database-references/)一下：
```js
original_id = ObjectId()
another_id = ObjectId()

db.places.insert({
    "_id": original_id,
    "name": "Broadway Center",
    "url": "bc.example.net"
})
db.places.insert({     
    "_id": another_id,     
    "name": "Another Center",     
    "url": "ab.example.net" 
})

db.people.insert({
    "name": "Erin",
    "places_id": original_id,
    "url":  "bc.example.net/Erin"
})
db.people.insert({     
    "name": "Mick",     
    "places_id": another_id,   
     "url":  "ab.example.net/Erin" })    
```

观察id可见，通过索引id就可以建立起索引了。



[^1]:https://www.tutorialspoint.com/mongodb/mongodb_query_document.htm



