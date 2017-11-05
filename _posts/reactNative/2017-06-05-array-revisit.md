---
layout: post
title:  "JavaScript 5. 数组再探"
categories: JavaScript
tags:  JavaScript  
author: Geng
---

* content
{:toc}

前面基础部分已经介绍过了数组, 这里再介绍一些高级的内容.

## 数组内的搜索
前面介绍过`indexOf/lastIndexOf`和`includes`

### find()
数组的`find()`方法返回满足**符合检验函数**的第一个元素, 否则返回`undefined`:






```javascript
function isBigEnough(element) {
  return element >= 15;
}

[12, 5, 8, 130, 44].find(isBigEnough); 
```




    130



这个方法语法为:`find(function(item, index, array)`, 其中的那个函数会作用在每一个元素上, 各个参数为:
* `item`就是元素.
* `index`是索引值.
* `array`是数组本身.

当然, 使用之前介绍的**箭头函数**更好:


```javascript
let users = [
  {id: 1, name: "John"},
  {id: 2, name: "Pete"},
  {id: 3, name: "Mary"}
];

let user = users.find(item => item.id == 1);

console.log(user.name); 
```

    John


其他两个参数很少会用到, 不做介绍了. `findIndex()`用法没啥区别, 唯一不一样的就是返回是索引而不是元素值.

如果想要返回所有符合的元素呢? 可以使用`filter()`

### filter()
用法和`find()`一样: `filter(function(item, index, array)`, 但是返回的符合元素组成的数组:


```javascript
let users = [
  {id: 1, name: "John"},
  {id: 2, name: "Pete"},
  {id: 3, name: "Mary"}
];

// returns array of the first two users
let someUsers = users.filter(item => item.id < 3);

console.log(someUsers); // 2
```

    [ { id: 1, name: 'John' }, { id: 2, name: 'Pete' } ]


## 数组的变换
可以想象成**空间的映射关系**

### map 映射
还是熟悉的味道:`map(function(item, index, array)`:


```javascript
let upperCase = ["Bilbo", "Gandalf", "Nazgul"].map( item => item.toUpperCase() );
console.log(upperCase);
```

    [ 'BILBO', 'GANDALF', 'NAZGUL' ]


可见, 这就是对每个元素进行某个操作, 然后返回操作后的结果.

### forEach
其实`forEach`不应该放在这里, 但是这个方法和`map`很像, 所以拿过来做一个比较. 用法与`map`没有区别, 唯一不同是它没有返回值, 仅仅是对每个元素进行某操作:


```javascript
["Bilbo", "Gandalf", "Nazgul"].map( item => console.log(item.toUpperCase()) );
```

    BILBO
    GANDALF
    NAZGUL





    [ undefined, undefined, undefined ]



### sort 排序
这个方法直接介绍过, 这里在返回来看看:


```javascript
let numArr = [ 1, 2, 15 ];
numArr.sort();
console.log(numArr);
```

    [ 1, 15, 2 ]


看上面结果, 神奇吗? 排序结果完全不对啊! 这是为什么?

因为`sort`默认里面要排序的是字符串, 对于字符串来说, 上面结果就没问题了.

那我想要对数字排序呢? 我们可以给`sort()`传递一个排序函数, 根据返回的正负来排序:


```javascript
function compareNumeric(a, b) {
  if (a > b) return 1;
  if (a == b) return 0;
  if (a < b) return -1;
}

let numArr = [ 1, 2, 15 ];

numArr.sort(compareNumeric);

console.log(numArr);  
```

    [ 1, 2, 15 ]


因为仅仅能判断正负即可, 所以可以简化为:


```javascript
numArr.sort( (a, b) => a - b );

console.log(numArr); 
```

    [ 1, 2, 15 ]


### 降维 reduce/reduceRight
我们使用`map()`和`forEach()`遍历数组, 使用某种方法处理数组, 但是并没有降低数组维度. 

这里, 我们可以使用` reduce/reduceRight`来降低数组维度, 就是使一个一维数组变为零维, 也就是一个数字, 语法为:

```js
let value = arr.reduce( function(previousValue, item, index, arr) { /*...*/ }, initialValue );
```

**回调函数**参数中的第二个开始的参数很熟悉了吧? 那么第一个参数呢?

* `previousValue`是之前调用结果的值.

另外`initialValue`就是整个运算的初始值.

直接上例子看:


```javascript
let arr = [1, 2, 3, 4, 5]

let sum = arr.reduce( (sum, current) => sum + current, 0 );

console.log(sum); 
```

    15


简单说, 就是`sum`的初始值为`0`, 第一次计算的时候, `current`是第一个元素, 然后用`sum`记录`sum + current`, 然后以此类推.

不过我更喜欢用**数学公式**的方式说明这个问题, 因为我觉得它本来就是这样啊,:

$$  S_k = 0, \  k = 0 \\ 
    S_k = S_{k-1} + N_k, \ k \neq 0
$$

这是一个递归公式, 其中\\(N = n_0, n_1, ... , n_m\\)是一个数组, 我们想要计算它的累加, \\(S_k\\)为前\\(k\\)个元素的和.

上面的`reduce()`语句的回调函数如果是任意一个函数, 初值为\\(i_0\\), 那么:

$$  S_k = i_0, \  k = 0 \\ 
    S_k = f(S_{k-1}, N_k), \ k \neq 0
$$

假设我要计算各个元素质积, 那么:


```javascript
let product = arr.reduce( (product, current) => product * current, 1 );

console.log(product); 
```

    120


#### 如果没有设初值, 那么初值为第一个元素, 然后从第二个元素开始计算
上面代码可以改写为:


```javascript
let sumNoInit = arr.reduce( (sum, current) => sum + current );
console.log(sumNoInit); 

let productNoInit = arr.reduce( (product, current) => product * current );
console.log(productNoInit); 
```

    15
    120


#### `reduceRight()`的区别仅仅是从最后一个(最右边)开始算起, 无他

## 可迭代对象(Iterables)
可迭代对象包含数组, 字符串等, 它们的特点就是可以使用`for..of`循环:


```javascript
let ages = [20, 30, 40, 50];
for(let age of ages) {
    console.log(age);
}
```

    20
    30
    40
    50



```javascript
let fullName = "Jim Clark Silver";
for(let name of fullName) {
    console.log(name);
}
```

    J
    i
    m
     
    C
    l
    a
    r
    k
     
    S
    i
    l
    v
    e
    r


可见, 对于数组, 是元素为单位的遍历; 对于字符串, 是字母为单位的遍历. 

### 似数组 (Array-likes)
似数组是有索引和长度的对象, 类似数组, 故而得名. 

### Array.from()
我们可以用`Array.from()`方法将一个**iterable**或者**array-like**转变为**Array**:


```javascript
let arrayLike = {
  0: "Hello",
  1: "World",
  length: 2
};

let arr = Array.from(arrayLike); 
console.log(arr)
console.log(arr.length)
```

    [ 'Hello', 'World' ]
    2


你还可以给`Array.from()`提供一个映射方法:


```javascript
let arrDouble = Array.from(arrayLike, ele => ele + ele);
console.log(arrDouble)
```

    [ 'HelloHello', 'WorldWorld' ]

