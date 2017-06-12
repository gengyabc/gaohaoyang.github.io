---
layout: post
title:  "JavaScript 4. 函数基础"
categories: ReactNative
tags:  JavaScript  
author: Geng
---

* content
{:toc}

我们使用变量和简单的对象封装了数据, 对于操作这些数据的方法, 我们可以使用函数来封装

## 函数基础

### 定义的第一种方法 -- 我是函数(函数声明)
这种方法上来就跟人说, 我是函数:





```javascript
function iAmBoy() {
    console.log('I am a boy');
    console.log('Hello, girls');
    return 'boy';
}

iAmBoy(); // 调用函数
var me = iAmBoy();  // 调用函数并获取返回值
```

    I am a boy
    Hello, girls
    I am a boy
    Hello, girls





    'boy'



### 定义的第二种方法 -- 我是变量(函数表达式)
上面的方法, 学过其他语言的同学应该理解了起来毫无压力, 那么我们看下面这种定义函数的方法:


```javascript
let iAmGirl = function() {
    console.log('I am a girl');
    console.log('Hello, boys');
    return 'girl';
};

iAmGirl();  // 调用方法
me = iAmGirl();  // 调用函数并获取返回值
```

    I am a girl
    Hello, boys
    I am a girl
    Hello, boys





    'girl'



第二种定义方法显然突出的是函数也是一种变量, 而且第一种定义方法和第二种方法**几乎等效**, 不过我个人认为, 对于理解JavaScript函数的很多特性, 第二种方法更方便理解这些特性.

采用第二种方法, 首先可以看到`iAmGirl`是一个变量:




```javascript
iAmGirl
```




    [Function: iAmGirl]



另一方面, 这个变量存储的是函数, 我们可以用`()`启动这个变量存储的函数:


```javascript
iAmGirl()
```

    I am a girl
    Hello, boys





    'girl'



> 需要注意到的是, iAmGirl这个变量存储的是一个匿名函数, 没有函数名.

## 参数
为了让函数更加灵活, 函数可以传递参数:


```javascript
let isAdult = function(age) {
    if (age >= 18) {
        return true;
    } else {
        return false;
    }
};

isAdult(18);
```




    true



也可以使用**默认参数值**, 如果没有对应参数, 那么使用默认值:


```javascript
let isAdult = function(age=1) {
    if (age >= 18) {
        return true;
    } else {
        return false;
    }
};

isAdult();
```




    false



### ES6以前的写法
了解下ES6之前的写法:

```js
function showMessage(from, text) {
  if (text === undefined) {
    text = 'no text given';
  }

  alert( from + ": " + text );
}
```

或者

```js
function showMessage(from, text) {
  // if text is falsy then text gets the "default" value
  text = text || 'no text given';
  ...
}
```

## 对象和函数
我们来看下对象作为函数的参数和返回值的例子.

### 对象在函数内

#### 对象作为参数



```javascript
let book = {
    title: 'wanwu.tech',
    author: 'geng',
    year: 2017
};

let showBook = function(book) {
    console.log( `${book.title} is writen by ${book.author} in ${book.year}`);
};

showBook(book);
```

    wanwu.tech is writen by geng in 2017





#### 对象作为返回值
既然对象是一种数据, 那么它自然也可以作为返回值:


```javascript
var writeBook = function() {
    return {
        title: 'wanwu.tech',
        author: 'geng',
        year: 2017
    }
}

var bookInfo = writeBook()

showBook(bookInfo)
```

    wanwu.tech is writen by geng in 2017





### 函数在方法内

#### 方法 --  对象内的函数
前面介绍的简单对象可以存储变量等数据, 也可以存储操作这些数据的方法. 在对象外部我们可以使用函数操作数据, 在对象内部, 我们称之为**方法**.


```javascript
var book = {
    title: 'wanwu.tech',
    author: 'geng',
    year: 2017,
    blankLine: function() {
        console.log('--------')
        console.log('\n')
        console.log('--------')
    }
}

book.blankLine()
```

    --------
    
    
    --------






#### 命名空间
包含数据,方法代码的JavaScript对象就是一个命名空间(namespace). 上面代码的`book`就是一个命名空间. 

比如我们可以在`book`中再添加一个方法:


```javascript
book.newLine = function(lineChar) {
    var line = ''
    for(var i = 0; i < 30; i++) {
       line = line + lineChar
    }
    console.log(line)
}

book.newLine('-')
```

    ------------------------------





## => 箭头函数
还有一种创建函数的方法, 就是`=>`, 即箭头函数

```js
let func = (arg1, arg2, ...argN) => expression
```

上面这段代码就等效于:

```js
let func = function(arg1, arg2, ...argN) {
  return expression;
}
```

我们来试试:


```javascript
let add = (a, b) => a + b;
console.log(add(1, 2));
```

    3


上面代码等效于:

```js
let add = function(a, b) {
  return a + b;
};
```

### 只有一个参数, 可以省略参数列表的括号


```javascript
let double = n => n * 2
console.log(double(2))
```

    4


### 没有参数的话, 需要空的括号


```javascript
let sayHi = () => console.log('Hi')
sayHi()
```

    Hi


### 较长的箭头函数要大括号和return


```javascript
let add = (a, b) => {  // 大括号说明这是多行函数
  let result = a + b;
  return result; // 需要用return
};

console.log( add(1, 2) ); // 3
```

    3


### 小练习
使用箭头函数修改下面代码:


```javascript
function areYouSure(isSure, yes, no) {
  if (isSure) yes()
  else no();
}

areYouSure(
  "你确定吗?",
  function() { console.log("我很确定"); },
  function() { console.log("我不知道"); }
);
```

    我很确定



```javascript
areYouSure(
  "你确定吗?",
  () => { console.log("我很确定"); },
  () => { console.log("我不知道"); }
);
```

    我很确定


## 回调函数callback
就是上面那些作为方法的参数传来传去的函数

## 应用解构赋值
就像是给传递的参数起了名字, 看代码:


```javascript
let parameters = {
  title: "My menu",
  items: ["Item1", "Item2"]
};

// 参数是一个对象, 可以立即展开为变量
function showMenu({title = "Untitled", width = 200, height = 100, items = []}) {
  // title, items – 来自parameters
  // width, height – 使用默认值
  console.log( title + ' ' + width + ' ' + height ); // My Menu 100 200
  console.log( items ); // Item1, Item2
}

showMenu(parameters);
```

    My menu 200 100
    [ 'Item1', 'Item2' ]

