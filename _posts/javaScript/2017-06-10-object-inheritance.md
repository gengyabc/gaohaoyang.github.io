---
layout: post
title:  "JavaScript 10. 对象 -- 继承"
categories: JavaScript
tags:  JavaScript  
author: Geng
---

* content
{:toc}


终于到继承了. 但是JavaScript几乎和所有其他语言的继承都不一样, 因为JavaScript没有类啊. JavaScript使用**原型继承**.

参考文献: 
* [F.prototype](https://javascript.info/function-prototype)
* [Class patterns](https://javascript.info/class-patterns)

## 原型继承 (prototypal inheritance)
JavaScript的对象有一个特殊的属性`[[Prototype]]`, 表示一种继承属性, 仅仅表示某对象从哪里继承, 它是内置的, 不可直接访问的. 不过可以使用`__proto__`属性访问, ES6开始可以分别用`Object.getPrototypeOf()`和`Object.setPrototypeOf()`来读写. 所以`__proto__`可以理解为`[[Prototype]]`的`getter`和`setter`.







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



和其他语言一样, JavaScript 继承也有类似的特征, 比如重写等, 总体来说没有太大区别.

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


可以发现, 我们没有设置过`rabbit`的`__proto__`, 但是设置过一个`Rabbit.prototype`属性, 那么这个是什么?

**当使用`new F()`时, 其生成的对象的`[[Prototype]]`就会设置为`F.prototype`**. 相当于在 😆 行后面还有一句:`rabbit.__proto__ == animal`

> 这里的`prototype`是函数的一个属性, 而`[[Prototype]]`是对象的隐含属性.

![]({{ site.url }}/assets/images/posts/react/native/2017-06-10-object-inheritance/pro1.png)

上图中, 纵向代表继承关系, 横向代表属性赋值. 可见, `rabbit` 从 `animal` 继承, `Rabbit` 有一个属性 `prototype` 指向 `animal`.  

事实上每个函数 `F` 都有一个默认 `prototype` 属性 (`F.prototype`). 而这个 `prototype` 有且仅有一个属性: `constructor`, 而且 `constructor` 指回函数 `F` 本身, 如下图所示:

![]({{ site.url }}/assets/images/posts/react/native/2017-06-10-object-inheritance/pro2.png)

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

![]({{ site.url }}/assets/images/posts/react/native/2017-06-10-object-inheritance/pro3.png)

如果我们不知道一个对象的的构造函数的话, 但是又想要创建一个同样类型的对象的话, 可以:


```javascript
function Rabbit(name) {
    this.name = name;
    console.log(name);
}

var rabbit = new Rabbit("white");
var rabbit2 = new rabbit.constructor("black");
```

    white
    black





    undefined



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


`Object.create(原型对象)` 建立一个新的空的对象, 这个新对象的 `[[prototype]]` 就是传入其中的对象.

## 类模式
建立对象的模式有多种, 和其他语言一样, JavaScript 也可以使用 `class` 创建所谓的类.

### 函数类模式


```javascript
function User(name) {
    this.sayHi = function() {
        console.log(`Hi, ${name}`);
    };
}

var user = new User('wanwu.tech');
user.sayHi();
```

    Hi, wanwu.tech





    undefined



这个模式可以创建一个"类":
* 它提供了一个创建对象的模板(使用 `new` 创建)
* 提供了状态的初始值(`name`)
* 提供了方法(`sayHi()`)

这种模式之前介绍对象的时候其实已经说过.

### 工厂模式

这种模式其实以前也介绍过, 这里只不过给一个名字罢了:


```javascript
function User(name, birthday) {
  // 私有
  function calcAge() {
    return new Date().getFullYear() - birthday.getFullYear();
  }

  return {
    sayHi() {
      console.log(name + ', age:' + calcAge());
    }
  };
}

var user = User("John", new Date(2000,0,1));
user.sayHi(); // John
```

    John, age:17





    undefined



这种模式返回对象, 唯一和函数类模式的区别是不用使用 `new` 来新建对象.

### 基于原型的对象(Prototype-based classes)
这里介绍相对最好一种: Prototype-based classes.


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


上面代码的构造函数 `User` 初始化了数据, 然后把方法赋值给了 `User.prototype`. 而且可以发现, 方法不在 `User` 方法中, 他们的词汇环境不一样, 比如 `sayHi()` 不能访问 `User` 内部的局部变量(现在并没有局部变量).

`_`: 没事别改, 但是非要改也不能拦着你.

这种模式, 方法被所有创建的对象共享, 每个对象单独持有其数据. 但是在其他两种模式中, 方法也是每个对象单独持有的, 比较浪费内存.

在"prototypal pattern"中, 所有方法都在 `User.prototype`, 被所有 user 对象共享. 对象本身之存储数据. 而且这种模式也方便我们设置继承. 


### 基于原型的继承

假设我们有两个基于原型的类:


```javascript
function Rabbit(name) {
  this.name = name;
}

Rabbit.prototype.jump = function() {
  console.log(this.name + ' jumps!');
};

var rabbit = new Rabbit("My rabbit");

////////////////二者不相关/////////////////////////

function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function() {
  console.log(this.name + ' eats.');
};

var animal = new Animal("My animal");
```




    [Function]



我们想要 Rabbit 继承 Animal, 应该做什么呢?

现在, rabbit 的方法定义在了 `Rabbit.prototype`, 我们希望 rabbit 在 `Rabbit.prototype` 中找不到某个方法时, 还能去 `Animal.prototype` 寻找, 所以, 我们想要的继承链为: **rabbit → Rabbit.prototype → Animal.prototype**.


```javascript
function Animal(name) {
  this.name = name;
}

Animal.prototype.eat = function() {
  console.log(this.name + ' eats.');
};

var animal = new Animal("My animal");

function Rabbit(name) {
  this.name = name;
}

// 这一步搞定了继承链
Rabbit.prototype.__proto__ = Animal.prototype;

Rabbit.prototype.jump = function() {
  console.log(this.name + ' jumps!');
};

var rabbit = new Rabbit("My rabbit");
rabbit.eat();
rabbit.jump();
```

    My rabbit eats.
    My rabbit jumps!





    undefined



我把这个继承关系表示为下图:

![]({{ site.url }}/assets/images/posts/react/native/2017-06-10-object-inheritance/pro4.png)


上面的内容熟悉了, 我们来写出来下图这种继承:

![](images\10 对象 -- 继承\subclassing_es6.jpg)

那么我们可以写出上图对象的代码:


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
静态方法属于 class 而不是 "prototype".


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
