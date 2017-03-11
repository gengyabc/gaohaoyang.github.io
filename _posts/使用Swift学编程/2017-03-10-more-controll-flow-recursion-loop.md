---
layout: post
title:  "7. for循环，递归与switch"
categories: 使用Swift学编程
tags:  swift 
author: Geng
---

* content
{:toc}

我们前面已经介绍过一些基本的控制程序流向的方法，这里这里我们介绍更多方法，方便以后使用。

更进一步，还会通过斐波那契数列，进一步熟悉递归和循环的关系。





## for循环
`for`循环可能会成为你以后最常用的循环手段，没有之一。那么，它与`while`循环有什么不同呢？我们前面的求和函数变为`for`循环来看下：

使用`while`循环：
```swift
func sum3From(_ from: Int, to: Int) -> Int {
    var result = 0
    var start = from
    
    while start <= to {
        result += start
        start += 1
    }
    
    return result
}
```

<br>

将上面函数变为`for`循环：
```swift
func sum4From(_ from: Int, to: Int) -> Int {
    var result = 0
    
    for num in from...to {
        result += num
    }

    return result
}
```
上面的`for`循环代码我们可以试着读出来：对于从**from**到**to**的所有数字**num**，我们采取以下操作。如果想语序更接近上面程序，我们可以使用英语读出：for every number **num** in the *range* from **from** to **to**, do the following thing(s).

可以感受到，这段代码的可读性也是很好的，在我不介绍基本语法的情况下，可能很多人已经能够猜出怎么使用了。

上面的代码不仅使用了`for`循环，还使用了一个我们以前没有碰到过的数据类型**范围**。

### 范围
首先，我们先介绍一下什么是**范围**（range）类型，然后再分析`for`语法。

**范围**分为两种，一种是闭合范围，相当于数学里的“[]”，Swift写为：
```swift
let closedRange = 0...5  // 包括（0 1 2 3 4 5）
```


一种是半开范围，相当于数学里的“[)”，Swift写为：
```swift
let halfOpenRange = 0..<5  // 包括（0 1 2 3 4）
```

> **范围**内的数字必须是递增的，也就是后面的数字要比前面的数字大。

### for详解
`for`循环语法如下：
```swift
for 局部常量 in 范围 {
    循环代码
}
```

对于我们刚才的这段代码：
```swift
func sum4From(_ from: Int, to: Int) -> Int {
    var result = 0
    
    for num in from...to {
        result += num
    }

    return result
}
```

`for`从**from**到**to**进行迭代，第一次迭代**num**为**from**。每次迭代，**num**都会增加，直到成为**to**为止（因为这里是闭合范围，所以最后一个**num**等于**to**）。

### 在某种条件下才循环
假设我们想要计算一个范围内奇数和，根据现有知识，可以如下操作：

```swift
let count = 10
var sum = 0
for i in 1...count {
    if i % 2 == 1 {
        sum += i
    }
}
```

不过，Swift为我们提供了一种更简洁，可读性更强的写法：
```swift
let count = 10
var sum = 0
for i in 1...count where i % 2 == 1 {
    sum += i
}
```
只有符合`where`从句的`i`才会进行循环。

### continue
我们前面介绍过一个`break`语法，现在再介绍一个`continue`。与`break`提前终止循环不同，`continue`只是跳出本次循环。它的功能其实很多情况下都可以用上面的`where`代替，但是还有些情况你想要更多控制，所以这里看下`continue`使用方法。

假设一个矩阵，其值是行号与列号的乘积，比如第三行第五列的值是15。现在，我们只想计算奇数行的值，如何用`continue`实现？

```swift
var sum = 0
for row in 0..<8 {
    if row % 2 == 0 {
        continue
    }
    for column in 0..<8 {
        sum += row * column
    }
}
```

尝试把`continue`替换为`break`，理解二者区别。

## 使用for循环计算斐波那契数列
这部分，使用`for`循环做一点有趣的事情，计算一下斐波那契数列。

[斐波那契数列](http://baike.baidu.com/item/%E6%96%90%E6%B3%A2%E9%82%A3%E5%A5%91%E6%95%B0%E5%88%97)长这个样： 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233，377，610，987，1597，2584，4181，6765，10946，17711，28657，46368........

这个数列从第3项开始，每一项都等于前两项之和。数学上可以写为：

$$ F(0)=1，F(1)=1, F(n)=F(n-1)+F(n-2) $$

### 递归求解
根据斐波那契数列的数学公式，我们很容易就可以写出一个递归的函数：
```swift
func fib(_ count: Int) -> Int {
    if count == 0 {
        return 0
    } else if count == 1 {
        return 1
    } else {
        return fib(count - 1) + fib(count - 2)
    }
}
```

从上面函数可以看出，`fib()`函数每次都会调用两次自己，这将会是一个巨大的资源消耗。

### 转换为尾递归
那么我们考虑怎么能把上面的递归变为尾递归，然后变为循环。

<br>

注意到`fib()`函数每次都会调用两次自己，我们将其看成是两个参数，**a**和**b**。

结合下图分析这个过程到底发生了什么：

> 下图是我自己总结的从视觉上考虑这个问题的方法，希望大家能够提供更好的解决方案：

![]({{ site.url }}/assets/images/posts/swift/2017-03-10-more-controll-flow-recursion-loop/1.png)

一共有两个参数，那么我在每个数的下面，都分别标记了**a**和**b**，表示这个数字的**a**和**b**两个参数。然后为了画图和思考方便，把\\(F(n)=F(n-1)+F(n-2)\\)转为\\(F(n+1)=F(n)+F(n-1)\\)。现在我们分析\\(F(n)\\)的两个参数。

注意排列方式，而且我已经将每个数字所属的参数用不同颜色的框标注出。那么这个图我是怎么做标记的呢？

如果后面数字的某个参数值没有任何改变地变换为前面数字那个参数的值，那么后面数字的那个参数在前面数字的那个参数正下面。我们需要一个参数指代自己，就是当前值，这里用**a**来指代。那么，上图中可见**a**在**b**下方，记为：`a <- b`。这就是一个**a**和**b**的**映射**关系。

如果某个参数通过某种运算得到，那么保持其上为空，方便我们自己做记录，这里为了清晰，我并没有做这样的记录。我们需要一个参数作为\\(F(n+1)=F(n)+F(n-1)\\)运算的结果，用**b**来指代，即\\(b(n)=F(n)\\)。那么\\(b(n+1)=b(n-1)+b(n)=a(n)+b(n)\\)（这里**a**和**b**的转换用到了上面**a**和**b**的**映射**关系）。比如**b**，可以在上面标记为：“a + b”，即表示这个**映射**为：`b <- a + b`。

这种图示方法比较适合这种参数不复杂的简单情况，从而为解决更复杂问题打好基础。

分析完两个参数，我们再看看上一章说的寄存器和指示器应该用什么来实现。从上面分析可见，**a**和**b**都可以作为寄存器，区别仅仅是当前数还是后一个数。那么指示器呢？直接使用要计算的斐波那契数列的位数（`count`）就可以了：

```swift
func fibIterate(_ count: Int) -> Int {
    func iterate(now: Int, next: Int, count: Int) -> Int {
        if count == 0 {
            return now
        } else {
            return iterate(now: next, next: now + next, count: count - 1)
        }
    }
    
    return iterate(now: 0, next: 1, count: count)
}
```

上面代码中，我们把**a**和**b**还有**count**作为参数代入`iterate(now:next:count:)`，当指示器(count)为0的时候，返回当前值（now），否则实现上文所说映射关系，进行尾递归。

<br>

### 转换为for循环
我们已经将递归转变为尾递归，下面我们再将其变为`for`循环：
```swift
func fibFor(_ count: Int) -> Int {
    var now = 0
    var next = 1
    for _ in 0..<count {
        let temp = now
        now = next
        next = temp + next
    }
    
    return now
}
```

上面代码注意两点：
1. 指示器是递增的，而不是尾递归中递减的。
2. `for`循环没有定义显示的局部常量，而是使用了`_`来代替。

第一点是因为**范围（range）**只能递增，不能递减，所以这里我们做了相应的调整。
第二点是因为我们只需要一个数字判断它是否在限定的范围内，循环体中并没有用到这个数字，所以可以使用`_`来代替。

上面代码中，当指示器等于`count`时，停止循环，并返回`now`。否则，进入循环并实现上文所说的映射关系。

### 练习1
试写出下面这个公式的递归，循环函数。

$$ f(n)=\begin{cases}n,& \text{if } n<3\\ 
f(n-1)+2f(n-2)+3f(n-3), & \text{if } n \geq 3 \end{cases} $$

如果有疑问，可以查看本章末尾提示。



## switch
下面再介绍另一种控制程序流向的方式：`switch`。

这是一个我们之前学习过的一个打印星期几的程序

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

现在我们把它变为`switch`来感受下区别:

```swift
let number = 1
let dayOfWeek: String

switch number {
case 1:
    dayOfWeek = "星期一"
case 2:
    dayOfWeek = "星期二"
case 3:
    dayOfWeek = "星期三"
case 4:
    dayOfWeek = "星期四"
case 5:
    dayOfWeek = "星期五"
case 6:
    dayOfWeek = "星期六"
case 7:
    dayOfWeek = "星期日"
default:
    dayOfWeek = "您输入的数字我看不懂"
}
print(dayOfWeek)
```

是不是感觉清爽了不少？

其中，`default`是说凡是不符合上面条件的情况，都采用`default`的操作。

你也可以使用`break`来结束`switch`，比如最后`default`可以写为：
```swift
default:
    break
}
```

> 如果有其他语言的编程经验，请注意Swift的`switch`语句不需要每个`case`都使用`break`。
> `default`不是必需的。

### 非数字情况
与很多编程语言不同，Swift条件判断可以使用非数字。而且，甚至如果几种情况的结果相同，你可以把他们合起来一起写：

```swift
let string = "Dog"
switch string {
case "Cat", "Dog":
    print("宠物")
default:
    print("可能不是宠物")
}
```

这个例子中，你使用字符串来判断。并且，如果是 "Cat"或"Dog"，执行相同代码。

## 总结
1. `for`循环。
2. `switch`语句

## 练习1题提示
映射关系：

last <- now

now <- next

next <- 3 * last + 2 * now + next

```swift
func myFunc2(count n: Int) -> Int {
    func iterate(last: Int, now: Int, next: Int, count: Int) -> Int {

    }
    
    return iterate(last: 0, now: 1, next: 2, count: n)
}
```

## 下一步
更多函数知识。
