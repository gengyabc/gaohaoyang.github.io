---
layout: post
title:  "JavaScript 6. 对象 -- 高级内容"
categories: JavaScript
tags:  JavaScript  
author: Geng
---

* content
{:toc}

这部分主要介绍一下构造函数和属性标记的问题. 

## 对象中包含函数
语法很简单, 直接上代码:





```javascript
let user = {
  sayHi: function() {
    console.log("Hello");
  }
};

user.sayHi()
```

    Hello


上面代码也可以简写:


```javascript
let user = {
  sayHi() {
    console.log("Hello");
  }
};

user.sayHi()
```

    Hello


## 使用函数创建对象
我们经常需要新建类似的对象, 比如博客会有许多博客帖子, 为了新建这些类似的内容, 我们可以使用函数流程化这个过程.

下面代码:


```javascript
var book = {
    title: 'wanwu.tech',
    author: 'geng',
    year: 2017
}

var showBook = function(book) {
    console.log( `${book.title} is writen by ${book.author} in ${book.year}`);
}

showBook(book);
```

    wanwu.tech is writen by geng in 2017





可以变为:


```javascript
var buildBook = function(title, author, year) {
   // 新建一个空对象
    var book = {};  
    
    // 为对象属性赋值
    book.title = title;
    book.author = author;
    book.year = year;
    
    // 返回新建的对象
    return book;
    
}

var book = buildBook('wanwu.tech', 'geng', 2017);

showBook(book);
```

    wanwu.tech is writen by geng in 2017





上面例子中, 我们使用了`buildBook()`来新建一个对象. 采用这种方法, 我们相当于首先设计了一个蓝图, 然后根据这个蓝图来构造我们的对象. 构造对象这个过程十分常用, 所以JavaScript本身提供了**构造函数**(constructor function).

## 构造函数
进入构造函数之前, 我们再看看上面的代码, 看看以我们现在的能力, 还能有什么改进.

### 创建一个方法属性
我们还可以为上面代码创建一个方法属性. 我们使用`showBook()`方法显示`book`, 但是这样需要使用外部的函数, 如果能调用自己的方法, 那就更好了. 以我们现在的知识储备, 我们可以这样来做:


```javascript
var buildBook = function(title, author, year) {
   // 新建一个空对象
    var book = {};  
    
    // 为对象属性赋值
    book.title = title;
    book.author = author;
    book.year = year;
    
    // 为对象添加一个方法
    book.show = function() {
        console.log( `${book.title} is writen by ${book.author} in ${book.year}`);
    }
    
    // 返回新建的对象
    return book;
    
}

var books = [
    buildBook('wanwu.tech', 'geng', 2017),
    buildBook('万物科技', 'geng', 2017),
    buildBook('万物', 'geng', 2017)
]

// 这里使用了数组的forEach方法, 其参数为一个函数, 这个函数对数组每个元素执行操作
books.forEach(function(book) {
    book.show();
});
```

    wanwu.tech is writen by geng in 2017
    万物科技 is writen by geng in 2017
    万物 is writen by geng in 2017





可见, 使用函数我们可以方便的建立大量对象. 那么如果我们使用**构造函数**如何实现呢?

### 使用构造函数
和其他语言类似, 我们使用`new`关键字, 这样做:

```js
book1 = new Book('wanwu.tech', 'geng', 2017)
book2 = new Book('万物科技', 'geng', 2017)
book3 = new Book('万物', 'geng', 2017)
```

也即是说, 想要让`Book`作为构造函数来工作, 就在函数前面加`new`就可以了. 一般来说, 大家习惯把构造函数的首字母大写, 这样大家一眼就知道你在做什么, 大家该怎么用了. 下面就是构造函数的写法:


```javascript
var Book = function(title, author, year) {
    
    // 使用了'this'，可以想象这里有一个隐藏的语句：var this = {}
    this.title = title;
    this.author = author;
    this.year = year;
    
    this.show = function() {
        console.log( `${this.title} is writen by ${this.author} in ${this.year}`);
    }

    // 没有使用return，但是你可以想象这里有一个隐藏的：return this 
}

var book = new Book('wanwu.tech', 'geng', 2017);
book.show()
```

    wanwu.tech is writen by geng in 2017




如果使用`new`来调用函数，那么在这个函数体中，Javascript会为你创建一个**空的对象**，而且会把它赋值给一个特殊变量**`this`**。你可以想象第一个行有一个隐藏的语句：
```js
var this = {}
```

然后你就可以像在`buildBook`中使用`book`变量那样使用`this`了。最后构造函数回自动返回`this`，所以你不用自己加一个`return`语句了。你也可以想象最后一行有一个隐藏的语句：
```js
return this
```

这里的`this`指向当前对象.

所以，`var book = new Book('wanwu.tech', 'geng', 2017)`的效果就相当于下面这个赋值：


```javascript
var book = {
    title: 'wanwu.tech',
    author:  'geng',
    year: 2017,    
    show: function() {
        console.log( `${this.title} is writen by ${this.author} in ${this.year}`);
    }
}
```




```javascript
book['title']
```




    'wanwu.tech'



## 更健壮的构造函数
使用构造函数的时候, 一定记得使用`new`. 说的轻松! 人非圣贤孰能无过, 万一忘了呢? 那么就会修改全局对象, 而不是新建的对象, 看代码:


```javascript
function Book(name, year) {
    this.name = name;
    this.year = year;
}

var myBook = Book("js book", 2017);  
console.log(myBook instanceof Book);  
console.log(global.name, global.year);  // 非浏览器中运行
//console.log(window.name, window.year); // 浏览器中运行

var myBook = new Book("js book", 2017);  
console.log(myBook instanceof Book);  
console.log(myBook.name, myBook.year);
```

    false
    js book 2017
    true
    js book 2017


如果没有`new`, 那么`Book`中的`this`成为`Windlow`(浏览器环境)或者`global`(非浏览器环境). 也就是我们不小心创建了两个全局变量: `name`和`year`. 

### 域安全的构造函数 (Scope-Safe Constructors)
那怎么解决人肯定会犯错的问题呢? 我们使用**Scope-Safe Constructors**:


```javascript
function Book(name) { 
    if (!(this instanceof Book)) {  // 判断this是否指向Book
        // 如果没有使用 "new".
        return new Book(name);
    } 
    
    this.name = name;
    this.year = year;
}

var myBook = Book("js book", 2017);  
console.log(myBook instanceof Book);  
console.log(global.name, global.year);  // 非浏览器中运行
//console.log(window.name, window.year); // 浏览器中运行

var myBook = new Book("js book", 2017);  
console.log(myBook instanceof Book);  
console.log(myBook.name, myBook.year);
```

    true
    js book 2017
    true
    js book 2017


还可以这样来做:


```javascript
function Book(name) { 
    if (!new.target) {  // 如果没有使用 "new".
        return new Book(name);
    } 
    
    this.name = name;
    this.year = year;
}

var myBook = Book("js book", 2017);  
console.log(myBook instanceof Book);  
console.log(global.name, global.year);  // 非浏览器中运行
//console.log(window.name, window.year); // 浏览器中运行

var myBook = new Book("js book", 2017);  
console.log(myBook instanceof Book);  
console.log(myBook.name, myBook.year);
```

    true
    js book 2017
    true
    js book 2017


很多内部类或者库都是用上述两种方法或者类似方法实现的, 以防止意想不到的事情发生:


```javascript
var withNew = new String('Hi')
console.log(withNew instanceof String)

var withoutNew = String('Hi')
console.log(withNew instanceof String)
```

    true
    true


## 属性标记
其他语言中, 我们可以设置对象属性是否可变等性质, JavaScript中使用**标记**(flag):

* writable – 可写
* enumerable – 可枚举
* configurable – 可删掉或修改

这三个属性默认都是`true`. 

### 读取标记
我们可以通过`Object.getOwnPropertyDescriptor(obj, propertyName)`或者`Object.getOwnPropertyDescriptors(obj)`来查看某个对象的**描述对象**(property descriptor object):


```javascript
let site = {
    name: '万物科技',
    url: 'wanwu.tech',
};
console.log(Object.getOwnPropertyDescriptor(site, 'name'));
```

    { value: '万物科技',
      writable: true,
      enumerable: true,
      configurable: true }



```javascript
console.log(Object.getOwnPropertyDescriptors(site));
```

    { name: 
       { value: '万物科技',
         writable: true,
         enumerable: true,
         configurable: true },
      url: 
       { value: 'wanwu.tech',
         writable: true,
         enumerable: true,
         configurable: true } }


可见, 每个对象属性的默认标记都是`true`. 

### 写入标记
使用`Object.defineProperty(obj, propertyName, descriptor)`来做:


```javascript
Object.defineProperty(site, 'name', {
    writable: false
});
console.log(Object.getOwnPropertyDescriptor(site, 'name'));
```

    { value: '万物科技',
      writable: false,
      enumerable: true,
      configurable: true }


可见, 我们可以通过`Object.defineProperty(obj, propertyName, descriptor)`或者`Object.defineProperties(obj, properties)`方便的改变描述对象.


```javascript
Object.defineProperty(site, 'siteName', {
    value: '万物'
});
console.log(Object.getOwnPropertyDescriptor(site, 'siteName'));
```

    { value: '万物',
      writable: false,
      enumerable: false,
      configurable: false }


这段代码中, 我们其实是给`site`增加了一个属性`siteName`, 但是除了`value`因为设置了值, 其他都默认为`false`. 


```javascript
Object.defineProperty(site, 'siteUrl', {
    writable: true
});
console.log(Object.getOwnPropertyDescriptor(site, 'siteUrl'));
```

    { value: undefined,
      writable: true,
      enumerable: false,
      configurable: false }


如果没有设置`value`, 那么就是`undefined`. 

下面在看看怎么使用`Object.defineProperties(obj, properties)`:


```javascript
Object.defineProperties(site, {
    'name': {
        value: 'wanwu',
        enumerable: false,
    }, 
    'siteUrl': {
        value: 'www.wanwu.tech',
    },
});

console.log(Object.getOwnPropertyDescriptors(site));
```

    { name: 
       { value: 'wanwu',
         writable: false,
         enumerable: false,
         configurable: true },
      url: 
       { value: 'wanwu.tech',
         writable: true,
         enumerable: true,
         configurable: true },
      siteName: 
       { value: '万物',
         writable: false,
         enumerable: false,
         configurable: false },
      siteUrl: 
       { value: 'www.wanwu.tech',
         writable: true,
         enumerable: false,
         configurable: false } }


### 只读属性
如果想要属性只读, 很简单, 设置`writable: false`即可. `configurable: false`也可以, 但是会导致`delete configuredProperty`不可使用.

## getter 和 setter
这东西高级语言大多数都有吧, 除了万能的`java`. 

JavaScript拥有两种不同的属性: 

* 数据属性: 我们一般用的属性都是这种, 着部分之前的所有属性都是数据属性.
* 访问属性: 类似Swift中的**计算属性**, 其实就是可以读写某个数据的函数, 但是外部看起来就是数据. 

用法很简单, 直接看代码:


```javascript
let user = {
  name: "Jon",
  surname: "Snow",

  get fullName() {
    return `${this.name} ${this.surname}`;
  },

  set fullName(value) {
    [this.name, this.surname] = value.split(" ");
  }
};

console.log(user.fullName);
```

    Jon Snow


### 访问描述对象
前面刚介绍了访问对象, 那么访问对象有类似的东西吗? 我们用`Object.getOwnPropertyDescriptors(obj)`来看看:


```javascript
Object.getOwnPropertyDescriptors(user);
```




    { name: 
       { value: 'Jon',
         writable: true,
         enumerable: true,
         configurable: true },
      surname: 
       { value: 'Snow',
         writable: true,
         enumerable: true,
         configurable: true },
      fullName: 
       { get: [Function: get fullName],
         set: [Function: set fullName],
         enumerable: true,
         configurable: true } }



可以发现, 访问对象也有描述对象, 但是这里有更专业的名词: **访问描述对象**. 与数据属性不同的一点是, `writable`变成了`get`和`set`.  我们试着修改一下访问描述对象:


```javascript
Object.defineProperty(user, 'fullName', {
  get() {
    return `${this.name} ${this.surname}, the bastard`;
  },

  set(value) {
    [this.name, this.surname] = value.split(" ");
  }
});

user.fullName
```




    'Jon Snow, the bastard'



但是, 在构造函数中不能使用getter和setter.
