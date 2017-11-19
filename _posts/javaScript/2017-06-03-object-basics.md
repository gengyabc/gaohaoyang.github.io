---
layout: post
title:  "JavaScript 3. 对象基础"
categories: JavaScript
tags:  JavaScript  
author: Geng
---

* content
{:toc}

作为一个面向对象语言，Javascript也有对象，了解了基础语法后，这部分介绍Javascript对象。但是与其他面向对象语言不同，Javascript没有其他语言的类.

## 基本语法
JavaScript建立对象的方法, 看来看去都像是其他语言的字典, 不过使用起来和其他语言差不多







```javascript
let book = {
    title: 'wanwu.tech',
    author: 'geng',
    year: 2017
};
console.log(book);
console.log(book.title);
console.log(typeof book);
```

    { title: 'wanwu.tech', author: 'geng', year: 2017 }
    wanwu.tech
    object




## 添加与删除
不过因为JavaScript的对象是动态类型, 在建立完对象后,还可以添加或删除属性:


```javascript
book.subTitle = 'tech and others';
console.log(book);
```

    { title: 'wanwu.tech',
      author: 'geng',
      year: 2017,
      subTitle: 'tech and others' }





```javascript
delete book.subTitle;
console.log(book);
```

    { title: 'wanwu.tech', author: 'geng', year: 2017 }




## 判断有无
可以使用`in`判断一个对象是否有某个属性:


```javascript
'title' in book;
```




    true



## 强大的`[]`

### 获取对象属性
我们已经使用过了`.`语法来获取某个属性的值。我们还可以使用`[]`来获取属性值。就如刚开始所说的，Javascript对象就像其他语言的字典一样：


```javascript
book['title'];
```




    'wanwu.tech'



那么这样做有什么好处呢？
* 我想添加一个属性'published date'怎么办？
如果使用点语法，那么没有办法做到，但是用`[]`就简单了：


```javascript
book['published date'] = 'future';
```




    'future'



* 动态的使用函数添加属性（关于函数将在下一部分介绍）：


```javascript
function getProperty(property) {
    var code = book[property];
    return code;
}
```





### 计算属性名
ES6开始, 支持了计算属性名. 这样, 属性名可以经计算得到:


```javascript
var param = 'size';
var config = {
  [param]: 12,
  ['mobile' + param.charAt(0).toUpperCase() + param.slice(1)]: 4
};

console.log(config); // {size: 12, mobileSize: 4}
```

    { size: 12, mobileSize: 4 }


可见, `config`的两个属性名都是基于其他属性得到的.

## 细节与杂项
还是, 有空就看, 或者当手册

### 属性值省写
先看代码:


```javascript
function makeUser(name, age) {
  return {
    name: name,
    age: age
  };
}

let user = makeUser("John", 30);
console.log(user)
```

    { name: 'John', age: 30 }


属性名和变量名一样, 而且这种情况还常有, 那么作为作为一个合格的程序员, 你肯定是很懒得, 懒人就会有懒办法:


```javascript
function makeUser(name, age) {
  return {
    name,
    age
  };
}

let user = makeUser("John", 30);
console.log(user)
```

    { name: 'John', age: 30 }


**属性名和变量名一样, 可以省略书写变量**

### 遍历对象所有属性
使用`for in`循环:


```javascript
let user = {
  name: "John",
  age: 30,
  isAdmin: true
};

for(let key in user) {
  // keys
  console.log( key );  // name, age, isAdmin
  // values for the keys
  console.log( user[key] ); // John, 30, true
}
```

    name
    John
    age
    30
    isAdmin
    true


因为对象可以看成一个字典, 这里使用`key`, 另一种命名方法也很常见: `for(let prop in obj)`

### const object
const设置一个常量, 比如:


```javascript
const noChange = 'Hi'
noChange = 'Hello'
```


    evalmachine.<anonymous>:2

    noChange = 'Hello'

             ^

    

    TypeError: Assignment to constant variable.

        at evalmachine.<anonymous>:2:10

        at ContextifyScript.Script.runInThisContext (vm.js:44:33)

        at Object.runInThisContext (vm.js:116:38)

        at run ([eval]:617:19)

        at onRunRequest ([eval]:388:22)

        at onMessage ([eval]:356:17)

        at emitTwo (events.js:125:13)

        at process.emit (events.js:213:7)

        at process.nextTick (internal/child_process.js:755:12)

        at _combinedTickCallback (internal/process/next_tick.js:95:7)


但是, 如果`const`修饰对象呢:


```javascript
const canIChange = {
    name: 'wanwu.tech',
    age: 1,
};

canIChange.name = '万物科技'
console.log(canIChange)

canIChange = {
    name: '万物科技',
    age: 1,
};
```

    { name: '万物科技', age: 1 }



    evalmachine.<anonymous>:9

    canIChange = {

               ^

    

    TypeError: Assignment to constant variable.

        at evalmachine.<anonymous>:9:12

        at ContextifyScript.Script.runInThisContext (vm.js:44:33)

        at Object.runInThisContext (vm.js:116:38)

        at run ([eval]:617:19)

        at onRunRequest ([eval]:388:22)

        at onMessage ([eval]:356:17)

        at emitTwo (events.js:125:13)

        at process.emit (events.js:213:7)

        at process.nextTick (internal/child_process.js:755:12)

        at _combinedTickCallback (internal/process/next_tick.js:95:7)


根据上面的输出和错误提示, 可见:
* 常量对象的属性值可变
* 常量对象不可以更改引用地址

其实根据其他语言知识, 很明显对象是**引用值**, 那么其`const`只是规定其引用地址不变, 而且其中的值并没有说不能变.

> 那如果我想让其中的属性值不可变怎么办?


```javascript
Object.defineProperty(canIChange, 'name', {
  writable: false
});

canIChange.name = '我要'
canIChange.name
```




    '万物科技'



具体方法不重要, 现在只是说可以这样做, 但是注意这里的改变失败悄声无息, 如果想让它报错, 可以: `"use strict";`

## JSON基础
这里看下: Object <--> String

* `JSON.stringify`: Object --> String
* `JSON.parse`: String --> Object


```javascript
let person = {
  name: '万物',
  age: 1,
  isAdmin: true,
  courses: ['html', 'css', 'js'],
};
let json = JSON.stringify(person);

console.log(typeof json); 
console.log('JSON encoded object:')
console.log(json);
console.log('\n');

console.log('JSON parsed Object:')
let reborn = JSON.parse(json);
console.log(typeof reborn);
console.log(reborn);
```

    string
    JSON encoded object:
    {"name":"万物","age":1,"isAdmin":true,"courses":["html","css","js"]}
    
    
    JSON parsed Object:
    object
    { name: '万物',
      age: 1,
      isAdmin: true,
      courses: [ 'html', 'css', 'js' ] }


上面代码可见, 使用很简单, 但是有几个细节要注意:

JSON-encoded object有几个特点:

* 所有字符串都是**双引号**
* 对象属性名也用双引号
