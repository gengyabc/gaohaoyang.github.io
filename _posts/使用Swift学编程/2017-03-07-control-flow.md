---
layout: post
title:  "5. 基本控制流"
categories: 使用Swift学编程
tags:  swift 
author: Geng
---

* content
{:toc}


世界是复杂的，不是一个计算苹果价格那么简单，也不是说一句“我叫韩梅梅”就能解决的。面对复杂的情况，我们一般都是分情况讨论，如果这样，就这样做。如果那样，就那样做。

下面，我们首先解决怎么判断各种**如果**的方法。

为了知道怎么判断，我们需要知道怎么做比较，并得出什么是真，什么是假的结论。





## 比较
比较，就是比大小，使用**比较操作符**。如何显示比较结果呢，我们使用**true**和**false**，即**真**和**假**来表示结果。这种数据类型是**Bool**（布尔）。只有两种可能，真或者假。

```swift
let yes = true
let no = false
```

上面代码即swift声明并初始化了两个布尔常量。注意这里使用了类型推断。

### 布尔操作符
知道了怎么表示真假，那么如何判断呢？无非就是我们数学学过那些，比如相等，大于，小于一类的。

`==`: 相等

`!=`：不等

`>`：大于

`>=`：大于等于

`<`：小于

`<=`：小于等于


我们观察下这些布尔操作的值是什么：

```swift
let isTrueOrFalse = (4 == 5)
```

上面简单的试验了一个`==`，观察`isTrueOrFalse`值是什么？然后试试其他几个布尔操作符。

### 布尔运算
有了基本的布尔操作符，我们再来看下布尔运算的问题。简单来说，就是**与**和**或**的问题。其中，**与**用`&&`，**或**用`||`表示。

关于布尔运算，可以参考[布尔运算](http://www.baike.com/wiki/%E5%B8%83%E5%B0%94%E8%BF%90%E7%AE%97)，这里不做过多介绍。

## 如果
熟悉了基本的布尔运算，下一步就需要把它们用起来了。

比如表达“a比b大，那么就打印a”：
```swift
let a = 10
let b = 1
if a > b {
    print(a)
}
```

这里，我们使用了条件语句`if`，中文就是**如果**的意思。
其语法为：

```swift
if 条件成立（true） {
    则执行片段1
}
if后的内容
```

流程图如下：

![]({{ site.url }}/assets/images/posts/swift/2017-03-07-control-flow/1.png)


可见，条件语句在`if`后的条件为**真**的时候，才会执行片段1。

如果表达“a比b大，那么就打印a，否则就打印b呢”，我们可以这样：

```swift
let a = 10
let b = 1
if a > b {
    print(a)
} else {
    print(b)
}
```

其语法为如`else`理解为`否则`：

```swift
if 条件1成立（true） {
    则执行片段1
} else {
    否则执行判断2
}
else后的内容
```

流程图如下：

![]({{ site.url }}/assets/images/posts/swift/2017-03-07-control-flow/2.png)

对应的还有[三元操作符](http://c.biancheng.net/cpp/html/2280.html)，有兴趣自己查看。

<br>

如果表达“a比b大，那么就打印a，否则如果a和b相等，打印等于号，其他情况都打印b呢”，我们可以这样：

```swift
let a = 10
let b = 1
if a > b {
    print(a)
} else if a == b{
    print("=")
}
else {
    print(b)
}
```

其语法为：

```swift
if 条件1成立（true） {
    则执行片段1
} else if  条件2成立（true） {
    执行片段2
} else if  条件3成立（true） {
    执行片段3
} 
后面内容
```
上面可以读作：如果条件1成立，那么执行片段1。否则（在条件1不成立的情况下）如果条件2成立，执行片段2。否则（在条件2不成立的情况下）如果条件3成立，执行片段3。最后执行后面内容。


其流程图为：

![]({{ site.url }}/assets/images/posts/swift/2017-03-07-control-flow/3.png)

### 练习
观察下面两个程序，判断打印什么？理解`if`和`else if`的区别

```swift
// 1
let a = 10
let b = 10
if a >= b {
    print(a)
} else if a == b{
    print("=")
} else {
    print(b)
}
```

```swift
// 2
let a = 10
let b = 10
if a >= b {
    print(a)
} 
if a == b{
    print("=")
} else {
    print(b)
}
```
### 嵌套if else
下面，我们使用上面知识，设计一个根据输入数字，输出星期几的小程序：

```swift
let number = 1
let dayOfWeek: String

if number == 1 {
    dayOfWeek = "星期一"
} else if number == 2 {
    dayOfWeek = "星期二"
} else if number == 3 {
    dayOfWeek = "星期三"
} else if number == 4 {
    dayOfWeek = "星期四"
} else if number == 5 {
    dayOfWeek = "星期五"
} else if number == 6 {
    dayOfWeek = "星期六"
} else if number == 7 {
    dayOfWeek = "星期日"
} else {
    dayOfWeek = "您输入的数字我看不懂"
}
print(dayOfWeek)
```

更改`number`，看看都会输出什么。

### 短路
如果`if`和布尔运算结合使用会发生什么呢？观察下面代码：

```swift
let name = "小明"
if 1 > 2 && name == "小明" {
    print(name)
}

if 1 < 2 || name == "小红" {
    print(name)
}
```

我们发现，对于`与`运算，`1 > 2`是假，那么不管后面的判断是真是假，并不影响整体的判断。所以后面的表达式不会执行。

类似的，对于`或`运算，`1 < 2`是真，那么不管后面的判断是真是假，并不影响整体的判断。所以后面的表达式不会执行。

### 封装变量或者常量
`if`引入了一个新的概念：**作用域**。
设想你去买苹果，一斤5块钱，但是如果你买5斤以上，那么新增的重量每斤4块钱。

```swift
let numberApple = 10
let price = 5
var totalPrice = 0

if numberApple <= 5 {
    totalPrice = numberApple * price
} else {
    let lowPrice = 4
    totalPrice = 5 * price
    totalPrice += (numberApple - 5) * lowPrice
}

print(totalPrice)
```

这段代码首先判断苹果数量。如果数量小于等于5，那么直接计算。否则，新建一个常量`lowPrice`，并设为4。总价等于两部分价格的总和。

注意，我们这里在`else`中引入了一个新的**局部常量**：`lowPrice`。尝试在这段代码最后写下面代码：

```swift
print(lowPrice)
```

我们发现如下报错：
> Use of unresolved identifier 'lowPrice'

这条错误说明提醒你`lowPrice`只能用在它所声明的范围内，这个范围就是**作用域**，其范围就是`else`后大括号包括的范围。超出这个范围，`lowPrice`不可见。

同时我们要注意到，在我们这个作用域的父作用域（也就是这个大括号外面的区域）声明的常量或变量，在此作用域仍然有效。

## 循环
假设我们要计算1到100的和，那么如果不用公式计算，应该怎么做呢？

```swift
var number = 2
let maxNum = 100
var sum = 1

while number <= maxNum {
    sum += number
    number += 1
}

print(sum)
```

这个过程可以理解为：number初始为第二个数字，maxNum是最大的数字，sum初值为第一个数字。然后先计算第一个与第二个数字之和并存为sum，再计算sum与第三个数字的和，一直计算到number变为100。

`while`语法为：
```swift
while <条件> {
	<循环代码E>
}
```

只要条件为真，循环代码就会循环往复执行。如果为假，那么就会跳出循环向后运行。

有时候，你可能想要提前终止循环，那么可以使用`break`。
```swift
var number = 2
let maxNum = 100
var sum = 1

while number <= maxNum {
    sum += number
    number += 1
    if sum > 100 {
    	break
    }
}

print(sum)
```

我们可以发现，一旦`sum`大于100，循环就会提前终止。

类似的，我们还可以使用`repeat`，其语法：
```swift
repeat {
	<循环代码>
} while <条件>
```

在这个结构中，先无条件执行循环代码，然后判断条件，如果成立，再次去执行循环代码，如果不成立，退出循环。

以上代码可改为：
```swift
var number = 2
let maxNum = 10
var sum = 1

repeat {
    sum += number
    number += 1
} while(number <= maxNum)

print(sum)
```

## 练习
计算1到100的阶乘

## 总结
1. 布尔操作
2. 条件判断
3. 循环

## 下一步
深入理解为什么使用循环
