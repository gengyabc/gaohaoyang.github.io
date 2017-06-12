---
layout: post
title:  "10. 对象 -- 继承"
categories: ReactNative
tags:  JavaScript ReactNative 
author: Geng
---

* content
{:toc}

终于到继承了. 但是JavaScript几乎和所有其他语言的继承都不一样, 因为JavaScript没有类啊. JavaScript使用**原型继承**.

参考文献: 
* [F.prototype](https://javascript.info/function-prototype)
* [Class patterns](https://javascript.info/class-patterns)





## 原型继承 (prototypal inheritance)
JavaScript的对象有一个特殊的属性`[[Prototype]]`, 表示一种继承属性, 仅仅表示某对象从哪里继承, 它是内置的, 不可直接访问的. 不过可以使用`__proto__`属性访问, ES6开始可以分别用`Object.getPrototypeOf()`和`Object.setPrototypeOf()`来读写. 所以`__proto__`可以理解为`[[Prototype]]`的getter和setter.


```javascript
let animal = {
  eats: true
};

let rabbit = {
  jumps: true
};

rabbit.__proto__ = animal;
console.log(rabbit.__proto__ === animal);

```

    true


那么, 根据**继承**这个概念, 我们应该能猜到, 访问`rabbit.eats`应该是没有问题的:


```javascript
rabbit.eats
```




    true



### prototype和[[Prototype]]
上面说过, 我们可以使用`__proto__`来读写`[[Prototype]]`. 看下面代码, 哪里设置了`__proto__`, 最后`__proto__`是什么猜的出来吗?


```javascript
let animal = {
  eats: true
};

function Rabbit(name) {
  this.name = name;
}

Rabbit.prototype = animal;

let rabbit = new Rabbit("White Rabbit"); //  😆 

console.log(rabbit.eats);
console.log(rabbit.__proto__ === animal);
```

    true
    true


可以发现, 我们没有设置过`__proto__`, 但是设置过一个`Rabbit.prototype`属性, 那么这个是什么?

**当使用`new F()`时, 其生成的对象的`[[Prototype]]`就会设置为`F.prototype`**. 相当于在 😆 行后面还有一句:`rabbit.__proto__ == animal`

> 这里的`prototype`是函数的一个公有属性, 而`[[Prototype]]`是对象的隐含属性.

![](https://javascript.info/article/function-prototype/proto-constructor-animal-rabbit@2x.png)

上图中, 纵向代表继承关系, 横向代表属性. 可见, `rabbit`从`animal`继承(`[[Prototype]]`表示继承关系), `Rabbit`有一个属性`prototype`指向`animal`.  

事实上每个函数`F`都有一个默认`prototype`属性(`F.prototype`). 而这个`prototype`有且仅有一个属性:`constructor`, 而且`constructor`指回函数`F`本身.

![](https://javascript.info/article/function-prototype/function-prototype-constructor@2x.png)

可以这样认为:

```js
function Rabbit() {}

/* default prototype
Rabbit.prototype = { constructor: Rabbit };
*/
```



```javascript
function Rabbit() {}
// by default:
// Rabbit.prototype = { constructor: Rabbit }

console.log( Rabbit.prototype.constructor == Rabbit ); // true
```

    true


`rabbit.constructor`通过`[[Prototype]]`会指向`Rabbit`:

![](https://javascript.info/article/function-prototype/rabbit-prototype-constructor@2x.png)

### 使用create()

使用`Object.create(原型对象)`可以建立一个新对象, 看代码:


```javascript
var protoObj = {
   foo: function() {
       console.log('bar');
   }
};

var newObj = Object.create( protoObj );
console.log(newObj.__proto__ === protoObj);
```

    true


`Object.create(原型对象)`建立一个新的空的对象, 这个新对象的[[prototype]]就是传入其中的对象.

## 类模式
建立对象的模式有多种, 这里介绍相对最好一种: Prototype-based classes.


```javascript
function User(name, birthday) {
  this._name = name;
  this._birthday = birthday;
}

User.prototype._calcAge = function() {
  return new Date().getFullYear() - this._birthday.getFullYear();
};

User.prototype.sayHi = function() {
  console.log(this._name + ', age:' + this._calcAge());
};

let user = new User("John", new Date(2000,0,1));
user.sayHi(); // John
```

    John, age:17


上面代码的构造函数`User`声明了数据, 然后把方法赋值给了`User.prototype`. 而且可以发现, 方法不在`User`方法中, 他们的词汇环境不一样, 比如 `sayHi()`不能访问`User`内部的局部变量(现在并没有局部变量).

`_`: 没事别改, 但是非要改也不能拦着你.

在"prototypal pattern"中, 所有方法都在`User.prototype`, 被所有user对象共享. 对象本身之存储数据. 而且这种模式也方便我们设置继承. 

上面的内容熟悉了, 我们来写出来下图这种继承:

![]({{ site.url }}/assets/images/posts/react/native/2017-06-10-object-inheritance/subclassing_es6.jpg)


花几分钟时间写一下, 不要看下面的代码, 不要作弊😆 :


```javascript
// 一个普通的点
function Point(x, y) {
    this.x = x;
    this.y = y;
}

// 原型上声明一个函数draw()
Point.prototype.draw = function() {
    console.log('draw the point');
};

// 一个有颜色的点
function ColorPoint(x, y, color) {
    this.x = x;
    this.y = y;
    this.color = color;
};

// 函数ColorPoint的原型属性的[[prototype]](就是new ColorPoint()生成的对象的[[prototype]])
// 指向Point.prototype
ColorPoint.prototype.__proto__ = Point.prototype;  // 🙄


// 原型上声明一个函数colorDraw()
ColorPoint.prototype.colorDraw = function() {
    console.log('draw the point with color' + this.color);
};  // 🍄



var cp = new ColorPoint(25, 8, 'green');

console.log(cp.__proto__ === ColorPoint.prototype)
cp.draw();
cp.colorDraw();
```

    true
    draw the point
    draw the point with colorgreen


### 使用Object.create(原型对象)

我们可以将 🙄 行使用`Object.create(原型对象)`:

```js
ColorPoint.prototype = Object.create(Point.prototype);  // 🙄
```

> 不过修改之后, 这句不能放在 🍄 之后

## 使用 class
这种方法让JavaScript的对象更亲民, 更像Java, 更好用了. 具体它有什么用呢? 提供了什么新功能吗?

**class没有提供任何新的东西, 它的功能就是把上面代码改成下面代码**:


```javascript
class Point {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }
    
    draw() {
        console.log('draw the point');
    }
}

class ColorPoint extends Point {  // extends说明继承
    constructor(x, y, color) {
        super(x, y);
        this.color = color;
    }
    
    colorDraw() {
        console.log('draw the point with color' + this.color);
    }
}

var cp = new ColorPoint(25, 8, 'green');
console.log(cp.__proto__ === ColorPoint.prototype)
cp.draw();
cp.colorDraw();
```

    true
    draw the point
    draw the point with colorgreen


语法没有什么特别的, 不过需要注意几点:

* 数据只在`constructor`中, 其他位置只能有方法. 也就是说数据存储在各个对象中, 方法存储在原型.
* 子对象`constructor`如果没有, 会有一个默认的. 如果有, 那么必须在使用`this`之前调用`super`. (有点像swift)


### 使用getter, setter


```javascript
class User {

  constructor(name) {
    // invokes the setter
    this.name = name;
  }

  get name() {
    return this._name;
  }

  set name(value) {
    if (value.length < 4) {
      console.log("Name too short.");
      return;
    }
    this._name = value;
  }

}

let user = new User("John");
console.log(user.name); // John

user = new User(""); // Name too short.
```

    John
    Name too short.





    User {}



### 静态方法(Static methods)
静态方法术语class而不是"prototype".


```javascript
class User {
  static staticMethod() {
    console.log(this === User);
  }
}

User.staticMethod(); // true
```

    true


其实这就是对函数的属性赋值:

```js
function User() { }

User.staticMethod = function() {
  console.log(this === User);
};
```

> JavaScript中的`this`就是**点**之前的对象 -- “object before dot” rule
