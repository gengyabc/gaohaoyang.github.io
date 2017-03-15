---
layout: post
title:  "8. 函数和闭包"
categories: 使用Swift学编程
tags:  swift 
author: Geng
---

* content
{:toc}


Swfit中，函数是一种数据类型，还是一种特殊的闭包。

这一章中，我们学习如何把函数像其他变量或常量一样使用，如何操作闭包。然后通过对闭包的使用，将程序从普遍变为具体，体验这种方法带给你的威力。





## 基本使用
假设我们要做一个四则运算的小程序，最终的计算使用一个函数来做，那么我们怎么跟这个函数说我们到底使用加法，减法，乘法还是除法呢？

我们可以使用一个变量来标记到底使用哪个方法。因为Swift的函数就是一种数据类型，那么我们完全可以把函数直接传过去。查看下面程序：

```swift
func add(_ a: Int, _ b: Int) -> Int {
    return a + b
}

func subtract(_ a:Int, _ b: Int) -> Int {
    return a - b
}

func calculateWith(function: (Int, Int) -> Int, _ a: Int, _ b: Int) -> Int {
    return function(a, b)
}

calculateWith(function: add, 14, 3)
calculateWith(function: subtract, 14, 3)
```

上面程序中，我们把加法和减法函数传给了最终计算的函数。体验一下这种使用方法。

需要注意两点：
1. 函数的类型是什么？
2. 怎么把函数作为数据类型来使用？

### 函数类型是什么
在上述程序后面，添加：
```swift
let funcAdd = add
```

在playground中查看`funcAdd`数据类型为：`(Int, Int) -> Int`。
这里，常量名为`funcAdd`，类型为`(Int, Int) -> Int`。而且这里`add`使用的时候，不需要括号。

### 怎么把函数作为数据类型来使用
尝试使用这个函数数据类型的常量：
```swift
funcAdd(4,6)
```

可以发现，使用方法和使用`add`完全一样，需要括号和参数。

我们可以这样理解括号的作用：调用函数使其工作。如果没有括号，仅仅是一个常量或者变量而已。

### 回头看 calculateWith(function:\_:\_:)
```swift
func calculateWith(function: (Int, Int) -> Int, _ a: Int, _ b: Int) -> Int {
    return function(a, b)
}
``` 

根据上面分析可见，`function`参数位置其参数类型为`(Int, Int) -> Int`，是一个函数，此函数需要两个`Int`参数，返回`Int`返回值。

### 练习
补充乘法和除法，并使用。

## 闭包
**闭包**（Closure）从名字上看，就是一个闭合的包裹。既然是一个包裹，那么里面当然可以有常量，变量等数据；又因为是闭合的，那么定义在**闭包**里的常量和变量只在**闭包**内部有效。不过，**闭包**可以访问，存储和操作外部的变量和常量，这个过程可以理解为**捕获**。

那么为什么说函数是一种特殊的闭包呢？简单来说，闭包是没有名字的函数。

### 基本语法
我们可以这样声明一个闭包：
```swift
var iAmClosure: (Int, Int) -> Int
```

这个闭包`iAmClosure`接收两个参数，还有一个返回值。看看是不是和上面函数的类型一样。所以简单理解：闭包是没有名字的函数。

那么怎么传递参数呢：
```swift
iAmClosure = { (a: Int, b: Int) -> Int in
    return a * b
}
```

注意变量在哪里？在大括号里面，而且其使用方法和函数调用类似。

而且在返回值后面，使用了`in`关键字，说明其后部分为函数体的代码了。

### 改写四则运算函数
上面四则运算函数，我们来使用**闭包**改写，看下效果：
```swift
calculateWith(function: { (a, b) -> Int in
    return a + b
}, 2, 8)
```

是不是有点眼晕？没关系，我们仔细看下，就发现其实很简单。

首先看看返回值是多少？是10吧？那么可以猜出，着一定是一个加法运算了吧。

哪里计算了加法呢？看到`return a + b`了吧。

好了，我们已经大概知道了上面函数的功能，现在来分析一下语法。

<br>

我们知道，这个四则运算函数有三个输入参数，第一个是函数或者闭包，后面两个是计算的参数。这里，我们将闭包直接写入到了第一个参数的位置，然后紧跟后两个参数。

这样写起来有些混乱，所以我们尝试这样改写：
```swift
func calculateWithClosure(_ a: Int, _ b: Int, function: (Int, Int) -> Int) -> Int {
    return function(a, b)
}

calculateWithClosure(4, 6) { (a, b) -> Int in
    return a + b
}
```

我们将闭包放在了函数的最后一个参数的位置。然后使用的时候，圆括号内只包含了两个`Int`参数，而将整个闭包放在了最后。这样改写之后，整个结构看起来更加直观。

我们可以这样理解闭包的工作：*a*和*b*分别是闭包的参数，闭包会返回`a + b`的值。*a*和*b*的值分别是外部函数的参数*4*和*6*。

## 通用的求和函数
在数学中，我们很熟悉这种求和公式：

$$ sum=\sum _{n=0}^{m}f(n) $$

我们几乎本能地知道，这个公式展开是这个样子：

$$ \sum _{n=0}^{m}f(n) = f(0) + f(1) + ... + f(m) $$

这样，不管\\(f(n) = n\\)或是\\(f(n) = n^{2}\\)，还是其他什么，我们都知道怎样使用这个计算公式。这里，我们把求和的对象**抽象**为了另一个公式\\(f(x)\\)。

在Swift中，我们也可以做到上述**抽象**。或许你已经发现，函数或者闭包就可以做到这种**抽象**。比如我们要求和，但是具体是什么的和？留给函数或者闭包去解决。

```swift
func sumFrom(_ from: Int, to: Int, f: (Int) -> Int) -> Int {
    var result = 0
    
    for num in from...to {
        result += f(num)
    }
    
    return result
}

sumFrom(0, to: 5) { (n) -> Int in
    return n * n
}
```

这里，我们使用闭包，抽象了求和的对象，使得我们的求和代码适用性更广泛。在这个过程中，我们通过闭包，使用一个普遍的抽象的函数进行了更加具体的计算。

## 从普遍到具体
上面的部分，我们初步体验了闭包的能力，尝试了从抽象到具体的一个简单过程。

这部分中，我们将函数和闭包作为参数传递，求解两个数学问题，一个是零点，一个是定点。在数学上，这两个点对所有函数都具有**普遍**意义，然后用这两个概念，求接**具体**方程的根。在Swift中，我们同样可以借用这种思想，先写出具有**普遍**意义的函数，然后使用这些函数，完成**具体**的任务。

<br>

求零点，就是求一个方程的根，应该大家都比较熟悉了。

定点是什么呢？定点满足下列条件：

$$ f(x) = x $$

也就是说，定点肯定也会：

$$ x = f(x) = f(f(x)) = f(f(f(x))) ... $$

### 使用二分法求根
第一个普遍意义的问题就是求根。我们使用二分法来求根。

二分法是一种简单粗暴的求解方程\\(f(x) = 0 \\)的根方法。对一个连续函数\\(f(x)\\)，如果两个点*a*和*b*满足\\(f(a) < 0 < f(b) \\)，那么\\(f(x) \\)在*a*和*b*之间必然有至少一个零点。

令*x*是*a*和*b*的平均值，计算\\(f(x)\\)。如果\\(f(x) > 0 \\)，那么零点必然在*a*和*x*之间。反之，如果\\(f(x) < 0 \\)，那么零点必然在*x*和*b*之间。如果我们不停地重复这个步骤，那么\\(f(x)\\)最终会在一个很小的范围内存在零点。如果我们认为这个范围足够小，那么搜索停止。

我们使用下面方法求解：
```swift
func zeroPoint(negative low: Double, positive high: Double, f: (Double) -> Double) -> String {

    let fLow = f(low)
    let fHigh = f(high)
    let average = (low + high) / 2
    let midPoint = f(average)
    
    if fLow == 0 {
        return "根是\(low)"
    }
    
    if fHigh == 0 {
        return "the root is \(high)"
    }
    
    if (fLow < 0 && fHigh < 0) || (fLow > 0 && fHigh > 0) {
        return " ❌ 函数值符号相同，请重试"
    }
    
    func isCloseEnough() -> Bool {
        if abs(fLow - fHigh) < 0.001 {
            return true
        } else {
            return false
        }
    }

    if isCloseEnough() {
        return "根是\(low)"
    } else if midPoint > 0 {
        return zeroPoint(negative: low, positive: average, f: f)
    } else if midPoint < 0 {
        return zeroPoint(negative: average, positive: high, f: f)
    } else {
        return "根是\(midPoint)"
    }
}

let root = zeroPoint(negative: 1, positive: 2) { (x) -> Double in
    return x * x * x - 2 * x - 3
}
```

上面的程序没有什么太多新的语法内容。一个需要注意的是`isCloseEnough()`可以访问外界的常量或变量。

最后返回`low`还是`high`无关紧要，因为它们两个已经足够接近。

那么我们考虑下怎么样把它转为循环呢？因为以上递归已经是一个尾递归，所以我们很容易就可以把它变为循环如下：
```swift
func zeroPointLoop(negative low: Double, positive high: Double, f: (Double) -> Double) -> String {
    
    var neg = low
    var pos = high
    var fNeg = f(neg)
    var fPos = f(pos)

    while abs(fNeg - fPos) > 0.001 {
        let average = (neg + pos) / 2
        let midPoint = f(average)
        
        if fNeg == 0 {
            return "根是\(neg)"
        }
        
        if fPos == 0 {
            return "the root is \(pos)"
        }
        
        if (fNeg < 0 && fPos < 0) || (fNeg > 0 && fPos > 0) {
            return " ❌ 函数值符号相同，请重试"
        }
        
        if midPoint > 0 {
            pos = average
        } else if midPoint < 0 {
            neg = average
        } else {
            return "根是\(neg)"
        }
        
        fNeg = f(neg)
        fPos = f(pos)
    }
    
    return "根是\(neg)"
}

let rootLoop = zeroPointLoop(negative: 1, positive: 2) { (x) -> Double in
    return x * x * x - 2 * x - 3
}
```

这里，注意需要在循环体中更新各个变量。

### 寻找函数定点
另一个普遍意义的问题就是定点。

前面介绍过，定点满足下列条件：

$$ f(x) = x $$

那么，我们可以使用下列性质来求定点：

$$ x = f(x) = f(f(x)) = f(f(f(x))) ... $$

重复下列运算，直到变化不大，即可得到定点：

$$ f(x), f(f(x)), f(f(f(x))), ... $$

写为Swift代码如下：
```swift
func fixedPoint(guess: Double, f: (Double) -> Double) -> Double {
    func isCloseEnough(_ num1: Double, _ num2: Double) -> Bool {
        if abs(num1 - num2) < 0.001 {
            return true
        } else {
            return false
        }
    }
    
    if isCloseEnough(guess, f(guess)) {
        return guess
    } else {
        return fixedPoint(guess: f(guess), f: f)
    }
}

fixedPoint(guess: 1) { (x) -> Double in
    return cos(x)
}
```

因为上述代码已经是尾递归，所以可以轻松变为循环：
```swift
func fixedPointLoop(guess: Double, f: (Double) -> Double) -> Double {
    
    var tryNum = guess
    
    func isCloseEnough(_ num1: Double, _ num2: Double) -> Bool {
        if abs(num1 - num2) < 0.001 {
            return true
        } else {
            return false
        }
    }
    
    while !isCloseEnough(tryNum, f(tryNum)) {
        tryNum = f(tryNum)
    }
    
    return tryNum
}

fixedPointLoop(guess: 1) { (x) -> Double in
    return cos(x)
}
```

### 满意条件
前面我们使用了`isCloseEnough(_:_:)`来判断两个点是否足够近，也就是我们是否满意。但是这个函数有一些问题。

考虑极小的数字和极大的数字。

看出问题了吗？对于极小的数字，它们可以轻易地满足足够近的条件。对于极大的数字，它们几乎很难满足条件。那应该怎么办呢？

我们可以从变化率角度考虑。如果变化率足够小，那么我们就认为满意了。

下面把`isCloseEnough(_:_:)`修改为使用变化率来判断。
```swift
func isCloseEnough(_ num1: Double, _ num2: Double) -> Bool {
    if abs((num1 - num2) / num1) < 0.001 {
        return true
    } else {
        return false
    }
}
```

### 求平方根
有了普遍意义的零点和定点知识，我们来看看怎么使用它们来解决具体问题。

前面章节中，我们使用简化的牛顿迭代法求解了平方根。可以看出，这些方法的基本思想都一样，都是重复修正猜测值，直到满足某种条件。

其实，我们也可以把平方根问题转换为定点问题。

观察这个函数：

$$ f(x)=\dfrac {a} {x} $$

$$ f(\sqrt{a})=\dfrac {a} {\sqrt{a}} = 
\sqrt{a} $$

说明，\\(\sqrt{a}\\)就是这个函数的定点。而且注意，这个时候的\\(x\\)值，就是*a*的平方跟，所以这个函数的定点，就是我们要求的平方根。

> 其实，如果换一个方法考虑，如果把这个函数作如下变化：$$ g(x)=f(x) - x=\dfrac {a} {x} - x $$,
原来求定点的问题就转变成了一个求根的问题。详细讨论见后。

下面我们使用Swift来求解这个定点问题，以求得平方根：
```swift
func mySqrt(_ num: Double) -> Double {
    return fixedPointLoop(guess: 1) { (x) -> Double in
        return num / x
    }
}
```

但是，不幸的是，上面这个方法是不收敛的。自己把*1*代入这个方程，试一试，是不是发现陷入了一个**震荡循环**中？

怎样能够让这个震荡慢慢趋于平衡呢？我们可以试着给这个震荡加入一点阻力，使其变为一个**减幅震荡**。因为我们要求的值在\\(x\\)和\\(\dfrac {num} {x}\\)之间，所以我们可以将下一个猜测值变为\\(\dfrac {1} {2}(x+ \dfrac {num} {x})\\)，而不是原先的\\(\dfrac {num} {x}\\)。这种方法的**减幅震荡**方法称为**阻尼平均**。

<br>

但是在我们的求定点的函数中，我们这样写的：

```swift
func fixedPoint(guess: Double, f: (Double) -> Double) -> Double {
    ....
    
    if isCloseEnough(guess, f(guess)) {
        return guess
    } else {
        return fixedPoint(guess: f(guess), f: f) // guess变为f(guess)
    }
}
```

```swift
func fixedPointLoop(guess: Double, f: (Double) -> Double) -> Double {
    
    var tryNum = guess
    ....
    while !isCloseEnough(tryNum, f(tryNum)) {
        tryNum = f(tryNum) // tryNum变为f(tryNum)
    }
    
    return tryNum
}
```

难道需要更改上面的代码吗？如果修改了，下次我们需要其他**减幅震荡**方法，难道还需要再次改动？显然这样做是不合适的，因为寻找定点的函数是一个有着普遍意义函数，这里不应该有太多针对具体情况的操作。所以我们应该将具体情况，封装在具体的函数中，比如这里的`mySqrt(_:)`。

我们将`mySqrt(_:)`变为如下形式：

```swift
func mySqrt(_ num: Double) -> Double {
    return fixedPointLoop(guess: 1) { (x) -> Double in
        return (num / x + x) / 2
    }
}

mySqrt(3)
```

这里，我们把求\\( f(x)=\dfrac {a} {x} \\)的定点，变为了求\\( f(x)=\dfrac {1} {2} (\dfrac {a} {x} + x) \\)的定点。

经过计算，这样两个方程的定点相同。

这样修改后，求平方根函数就可以正常运行了。可以发现，这个求平方根的函数和前面介绍过的方法完全一样的，但却是从两个完全不同的方面考虑的。

## 闭包作为返回值
上面的例子中，我们将闭包和函数作为参数传递，简化了函数，而且增强了函数的表达能力。使用闭包作为返回值，我们可以将函数的表达能力进一步提高。

### 阻尼平均
上面示例代码中我们使用了**阻尼平均**的方法，这里，我们可以将这个方法写为一个函数，这个函数返回另一个函数：

```swift
func averageDamp(function f: @escaping (Double) -> Double) -> (Double) -> Double{
    func average(_ num: Double) -> Double {
        return (f(num) + num) / 2
    }
    
    return average
}
```

> 上面代码增加了`@escaping`，这个是Swift的一个闭包语法，不过这里使用`@escaping`好像是一个bug（[Nested functions should allow escaping attributes, be @noescape by default](https://bugs.swift.org/browse/SR-2274)）。

观察上面的函数，`average(_:)`定义了之后，直接返回，并没有太大必要使用函数，我们可以使用闭包来实现：

```swift
func averageDamp(function f: @escaping (Double) -> Double) -> (Double) -> Double{
    return { (x) -> Double in
        return (f(x) + x) / 2
    }
}
```

上面代码中，闭包接收一个参数，然后取平均值。

使用这个方法，我们改写一下上面求平方根的函数：

```swift
func mySqrt2(_ num: Double) -> Double {
    return fixedPointLoop(guess: 1, f: averageDamp(function: { (x) -> Double in
        return num / x
    }))
}
```

如果闭包中只有一个表达式，那么可以省略`return`关键字，上面代码可写为：
```swift
func mySqrt2(_ num: Double) -> Double {
    return fixedPointLoop(guess: 1, f: averageDamp(function: { (x) -> Double in num / x }))
}

mySqrt2(3)
```

上面代码非常清楚地表达了以下三个思想：定点搜索，阻尼平均和方程\\( f(x)=\dfrac {a} {x} \\)。与之前的求平方根方法相比，这个方法更加抽象，更加通用，更加具有表达力。因为它说明了我们要做定点搜索（通用的），使用阻尼平均方法（具体手段），对方程\\( f(x) \\)（具体被计算的对象）进行计算。

### 练习
使用上面方法，计算立方根。注意立方根是方程\\( f(x)=\dfrac {a} {x^{2}} \\)的定点。

### 牛顿迭代法
我们说过，前面求平方根的方法其实是一种简化的[牛顿迭代法](http://baike.baidu.com/item/%E7%89%9B%E9%A1%BF%E8%BF%AD%E4%BB%A3%E6%B3%95)的一个特例。这里我们简要说下[牛顿法求解和定点的关系](http://home.iitk.ac.in/~psraj/mth101/lecture_notes/lecture8.pdf)。

#### 求根
我们要求方程\\(f(x) = 0\\)的近似解。那么和我们可以考虑使用**定点**来求解。

我们把上面的方程\\(f(x) = 0\\)改写为\\(x = g(x)\\)，即\\(f(x) = g(x)-x\\)，那么，\\(x = g(x)\\)的解，也就是\\(g(x)\\)的定点，也是\\(f(x) = 0\\)的解。

那么考虑下面这个算法：

$$ x_{n+1}=g(x_{n}), \text{ n=0,1,2,...}$$

如果\\(f(x)\\)是连续的而且\\(x_{n}\\)收敛于\\(l_{0}\\)，那么\\(l_{0}\\)就是\\(g(x)\\)的定点，也是\\(f(x) = 0\\)的解。

注意到牛顿迭代法的公式为：

$$ x_{n+1} = x_{n} - \dfrac {f(x_{n})} {f'(x_{n})} $$

令\\(g(x) = x - \dfrac {f(x)} {f'(x)} \\)，观察上面方程可见，如果数学上的前提条件成立，那么牛顿迭代法的公式是\\(x = g(x)\\)的一个特殊形式。

可见，牛顿迭代法是求\\(g(x)\\)的定点，\\(f(x) = 0\\)的解。

如果初始猜测值选择合适，牛顿迭代法可以很快收敛。

#### 平方根求解再探
我们说过这个方程\\( g(x)=\dfrac {a} {x} \\)的定点就是\\(f(x)=\sqrt{x}\\)的根。我们之前只是证明了这个事实，但是并没有说明如何得到这个事实，下面我们尝试将它推导出来，并且看出**阻尼平均**就是牛顿迭代法在求平方根的时候的表现形式。

首先，我们来看怎么推导下面方程的根：

$$ f(x)=x^{2} - a $$

即：

$$ x^{2} = a $$


两边同时除以*x*:

$$ x = \dfrac {a} {x} $$

令\\(g(x) = \dfrac {a} {x}\\)，那么\\(g(x)\\)的定点就是\\(f(x)\\)的根。然后我们就可以使用**阻尼平均**的方法来求解了。

那么，**阻尼平均**和牛顿迭代法什么联系呢？我们将\\(f(x)=x^{2} - a\\)代入\\(g(x) = x - \dfrac {f(x)} {f'(x)} \\)，可得：

$$ g(x)=\dfrac {1} {2}(x + \dfrac {a} {x}) $$

即我们介绍的**阻尼平均**的形式。

#### 使用牛顿迭代法
为了使用牛顿迭代法，我们首先要知道怎么计算导数。数学上，导数定义是这样的：

$$ f'(x) = \dfrac {f(x +dx)-g(x)} {dx} $$

其中，*dx*是一个很小的数。

那么，我们可以把求导写为以下Swift代码：

```swift
func deriv(function f:@escaping (Double) -> (Double)) -> (Double) -> Double {
    let dx = 0.00001
    return { (x) -> Double in
        return (f(x + dx) - f(x))/dx
    }
}

deriv { (x) -> (Double) in
    x * x * x
}(5)
```

有了求导的能力，那么我们就可以开始使用牛顿迭代法了。观察下面牛顿迭代法：

$$ g(x) = x - \dfrac {f(x)} {f'(x)} $$

我们可以把以上迭代看作是一个函数变换的过程，即\\(f(x) -> g(x)\\)。我们将上面变换写为Swift函数：

```swift
func newtonTranform(function f: @escaping (Double) -> (Double)) -> (Double) -> Double {
    return { (x) -> Double in
        return x - (f(x) / deriv(function: f)(x))  // 返回g(x)
    }
}
```

那么根据前面分析，牛顿迭代法求解*f(x)*的根，就是求解 *g(x)*的定点，写为Swift函数：

```swift
func newtonMethod(guess: Double, function f: @escaping (Double) -> Double) -> Double {
    return fixedPointLoop(guess: guess, f: newtonTranform(function: f))
}
```

那么，如果还是求解平方根的话，我们还可以这样写：

```swift
func mySqrt3(_ num: Double) -> Double {
    return newtonMethod(guess: 1) { (x) -> Double in
        x * x - num
    }
}

mySqrt3(4)
```

## 总结
求解平方根是一个具体的问题，这里我们使用了两种更加普遍的方法对它进行了求解，一个是定点方法，一个是牛顿迭代法。因为牛顿得代法本身和定点法的紧密关系，我们实际上学习了两种求解定点的方法。两个方法都是寻找一个函数的某种变换的定点。

这种普遍的思想写为Swift代码如下：
```swift
func fixedPointOfTransform(guess: Double, transform: (@escaping(Double) -> Double) -> (Double) -> Double, function f:@escaping (Double) -> Double) -> Double {
    return fixedPointLoop(guess: guess, f: transform(f))
}
```
上面这个函数中，第一个参数是初始的猜测值，第二个参数是转换公式（也是一个函数），第三个参数是要求定点的函数，最后返回定点值。

如果使用上面函数求解平方根，那么可以这样：
```swift
func mySqrt4(_ num: Double) -> Double {
    return fixedPointOfTransform(guess: 1, transform: averageDamp, function: { (x) -> Double in
        num / x
    })
}

mySqrt4(4)
```
根据前面知识，我们代入合适参数，就可以计算了。上面使用`fixedPointOfTransform(guess:transform:function)`，初始猜测值为*1*，使用变换方法为**阻尼平均**，要求的定点是方程\\(num / x\\)。

类似的，如果我们想使用**牛顿迭代法**，那么可以如下操作：
```swift
func mySqrt5(_ num: Double) -> Double {
    return fixedPointOfTransform(guess: 1, transform: newtonTranform, function: { (x) -> Double in
        x * x - num
    })
}

mySqrt5(4)
```

可见，通过使用闭包和函数作为参数和返回值，我们实现了从普遍到具体的变化。作为程序员，我们应该锻炼识别程序中普遍的抽象的内容的能力，然后可以在其上实现更具体的方案，也可以想想我们是否可以继续将这个抽象的内容进一步抽象。

闭包在Swift中是一种数据类型，意味着我们可以像操作其他变量或者常量一样操作它。
