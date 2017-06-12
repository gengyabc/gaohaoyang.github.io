---
layout: post
title:  "JavaScript 8. Map和Set"
categories: ReactNative
tags:  JavaScript  
author: Geng
---

* content
{:toc}

## Map 其实就是字典
其他语言叫**字典**, JavaScript叫Map, 我们看看它主要有什么方法:
* `new Map()` – 新建一个map
* `map.set(key, value)` – 根据key设置值
* `map.get(key)` – 根据key返回值
* `map.has(key)` – 判断是否有key
* `map.delete(key)` – 删除key
* `map.clear()` – 清空本map
* `map.size` – 本map大小






上代码:


```javascript
let map = new Map();

map.set('1', 'str1');   // a string key
map.set(1, 'num1');     // a numeric key
map.set(true, 'bool1'); // a boolean key

console.log( map.get(1)   ); 
console.log( map.get('1') ); 
console.log( map.get(true) ); 

console.log( map.size ); // 3
```

    num1
    str1
    bool1
    3


### 对象作为key


```javascript
let john = { name: "John" };

// 每个用户都存储这个访问计数
let visitsCountMap = new Map();

// john是key
visitsCountMap.set(john, 123);

console.log( visitsCountMap.get(john) ); 
```

    123


### set返回自己, 所以可以连锁调用

```js
map.set('2', 'str2')
  .set(2, 'num2')
  .set(false, 'bool2');
```

### 使用数组建立map


```javascript
let map = new Map([
  ['1',  'str1'],
  [1,    'num1'],
  [true, 'bool1']
]);

console.log(map)
```

    Map { '1' => 'str1', 1 => 'num1', true => 'bool1' }


### 遍历map
可以使用下面中的一种方法:

* `map.keys()` – 返回key的可遍历对象.
* `map.values()` – 返回值得可遍历对象.
* `map.entries()` – 返回[key, value]可遍历对象, `for..of`默认使用这个.


```javascript
let recipeMap = new Map([
  ['cucumber', 500],
  ['tomatoes', 350],
  ['onion',    50]
]);

console.log('by key:')
for(let vegetable of recipeMap.keys()) {
  console.log(vegetable); 
}
console.log('\n')

console.log('by value:')
for(let amount of recipeMap.values()) {
  console.log(amount); 
}
console.log('\n')

console.log('by [key value]:')
for(let entry of recipeMap) { // 同recipeMap.entries()
  console.log(entry); 
}
```

    by key:
    cucumber
    tomatoes
    onion
    
    
    by value:
    500
    350
    50
    
    
    by [key value]:
    [ 'cucumber', 500 ]
    [ 'tomatoes', 350 ]
    [ 'onion', 50 ]


> `map`也有`forEach()`方法, 试试吧

## Set 集合
值不可以重复, 其他语言也是这个名字吧?

主要方法有:
* `new Set(iterable)` – 建立一个Set, 可以从任意一个可遍历对象建立.
* `set.add(value)` – 添加一个值, 返回set本身, 说明可以连锁调用.
* `set.delete(value)` – 删除值, 删除前存在返回true, 否则false
* `set.has(value)` – 判断有误
* `set.clear()` – 清空
* `set.size` – 大小

我们来看看一段话有多少不重复字符:


```javascript
let news = `ASTANA - Chinese President Xi Jinping and his Kazakh counterpart, Nursultan Nazarbayev, vowed Thursday to accelerate the alignment of their countries' respective development policies and push bilateral ties to a higher level.

During their talks in the Kazakh capital of Astana, the two presidents agreed to further integrate the China-proposed Belt and Road Initiative and Kazakhstan's development strategy of "Nurly Zhol," or "Bright Path" in the Kazakh language.

The two countries will focus on aligning the New Eurasian Land Bridge and the construction of an economic corridor linking China, central Asia and west Asia, with the Kazakh strategy of building an international logistics corridor.

Meanwhile, they will join efforts in connecting the international production capacity cooperation with Kazakhstan's push for industrialization, sharing China's transportation advantages on land and by sea with the world's biggest landlocked country, and linking China's "digital Silk Road" initiative with the "digital Kazakhstan" strategy.

Xi and Nazarbayev also hailed the "great achievements" made in bilateral ties since China and Kazakhstan forged diplomatic relations 25 years ago and decided to push the healthy and stable development of their comprehensive strategic partnership to a higher level, so as to better serve the two peoples.

Xi attributed the development of bilateral ties by leaps and bounds in a relatively short period to the spirit of good neighborliness, mutual benefit and mutual support upheld by the two countries, the principles of mutual respect and equality, as well as their generations of friendship and the concept of common development.`;

let newsArray = news.split(' '); // 以空格为分隔符
console.log(`newsArray's length is ${newsArray.length}`);

let newsSet = new Set(newsArray);  // 使用Set消除重复
console.log(`newsArray's length is ${newsSet.size}`);
```

    newsArray's length is 243
    newsArray's length is 151


上面例子, 使用`Set`很轻松吧?

### 遍历
使用`for..of`或者`forEach`都可以


```javascript
let set = new Set(["oranges", "apples", "bananas"]);

for(let value of set) console.log(value);

set.forEach((value, valueAgain, set) => {
  console.log(value);
});
```

    oranges
    apples
    bananas
    oranges
    apples
    bananas


但是`forEach`的`valueAgain`是什么鬼? 为了和`Map`的`forEeah`兼容.

所以我们可以看到这些方法:

* `set.keys()` – 返回每一个值
* `set.values()` – 同上
* `set.entries()` – 返回[value, value]

