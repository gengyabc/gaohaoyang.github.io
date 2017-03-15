---
layout: post
title:  "6. 函数，递归，循环"
categories: 使用Swift学编程
tags:  swift 
author: Geng
---

* content
{:toc}


到目前为止，我们已经具备了编程的最基础知识。我们可以建立自己需要的常量或者变量，可以按照自己的思路做简单的任务，可以根据条件选择做事的手段，也可以重复做一件事情。为了进一步抽象你的代码，我们引入函数的感念。

函数是整个程序的灵魂，它定义了一段有某种功能的代码。编程语言中的函数类似数学中的公式。一方面，函数可以优化你的代码，另一方面，函数也可以提高你的程序的可读性。

这一章，将初步学习函数的知识，然后通过函数，理解递归，并进一步理解递归和循环的关系。






## 函数基础
假设你经常要打印自己的名字，那么你可以把这段代码抽象为一个函数：

```swift
func printMyName() {
    print("我叫小明")
}
```
上面是**函数定义**的一段代码。语法如下：

```swift
func 函数名() {
    代码
}
```

如果想使用这个函数，可以这样：
```swift
printMyName()
```

自己试试什么效果。

那么，你觉得我们一直在用的`print`是什么？对了，也是一个函数。

### 参数
到现在为止，函数只是静止的一段功能块，比如上一章计算1到100的和，我们可以包装为一个函数：

```swift
func sum() {
    var number = 2
    let maxNum = 100
    var s = 1
    
    while number <= maxNum {
        s += number
        number += 1
    }
    
    print(s)
}
```

但是你想让他真的和数学公式一样工作，还需要能够自定义一些**参数**传给它，还能**返回**一个结果，比如求从1到*m*的和的数学表达式如下：

$$ sum=\sum _{n=1}^{m}n $$

我们要自己代入*m*的值，还能得到这个运算返回的结果。那么如果我今天想要算1到1000呢？直接把*m*设为1000即可，其他完全不需要变化。这个在编程语言中，就是一个**参数化**的问题。

### 参数化
我们可以这样参数化我们求和程序，并提供一个**返回值**：

```swift
func sumFrom1(to maxNum: Int) -> Int {
    var number = 2
    var s = 1
    
    while number <= maxNum {
        s += number
        number += 1
    }
    
    return s
}

let sum1 = sumFrom1(to: 100)
let sum2 = sumFrom1(to: 10)
let sum3 = sumFrom1(to: 1000)
```

我们试着读出`func sumFrom1(to maxNum: Int) -> Int`：求和函数接受一个类型为Int的参数maxNum，返回一个Int类型的返回值。因为编写Swift的人是美国人，所以如果你用英语读的话，可能会豁然开朗：This function caculates the sum from 1 to maxNum and returns a value。

语法可以写作：
```swift
func 函数(实参标签 参数名: 参数类型) -> 返回值类型 {
    代码，其中可使用参数名
    
    return 返回值
}

函数(实参标签: 参数)
```

可以看出，为了表达一个参数，我们提供了三个信息：**实参标签**，**参数名**和**参数类型**。在函数内，我们通过使用**参数名**来指代这个参数，即**形参**。在调用函数的时候，我们通过**实参标签**来传递**实参**到**形参**。返回值使用`return`关键字返回给外部以便使用。

> 值得注意的是，上面求和函数的函数名不是`sumFrom1`，而是`sumFrom1(to:)`，也就是说，函数名是`函数(实参标签:)`。

### 可读性与简单性
通过为一个参数使用三个信息，我们可以尽可能的提高函数的可读性。但是，有的时候，这样写并没有对可读性有大幅提升，却使函数名称过长，不易交流，为了解决这个问题，Swift允许我们省略书写**实参标签**，比如写为这样：
```swift
func sumFrom1(to: Int) -> Int {
    var number = 2
    var s = 1
    
    while number <= to {
        s += number
        number += 1
    }
    
    return s
}

let sum1 = sumFrom1(to: 100)
let sum2 = sumFrom1(to: 10)
let sum3 = sumFrom1(to: 1000)
```

这个情况下，**实参标签**等于**参数名**，同时注意函数内代码的变化。

<br>

在个别情况下，我们甚至觉得在外部调用函数的时候不需要**实参标签**，我们可以在**实参标签**位置使用下划线`_`：

```swift
func sumFrom1(_ to: Int) -> Int {
    var number = 2
    var s = 1
    
    while number <= to {
        s += number
        number += 1
    }
    
    return s
}

let sum1 = sumFrom1(100)
let sum2 = sumFrom1(10)
let sum3 = sumFrom1(1000)
```

<br>

但是我们现在这个函数的情况中，不使用**实参标签**已经影响到了程序的可读性，所以我个人认为在这个函数中，这样修改不太好。

但是下面的加一功能的程序，不使用**实参标签**完全不会影响可读性：

```swift
func addOneTo(_ num: Int) -> Int{
    return num + 1
}

let result = addOneTo(3)
```

所以，到底怎么用，主要还是在简单性和可读性之间取一个最优值得问题。

> 上面例子中，返回值的位置我直接使用了一个计算表达式

### 参数列表
我们还可以向函数中提供更多参数，那么参数就变成了参数列表，比如计算两个数之和：

```swift
func add(_ a: Int, _ b: Int) -> Int {
    return a + b
}

let sumOfTwo = add(3, 4)
```

这里，我们没有使用**实参标签**，并没有影响可读性，如果添加**实参标签**，就变成了：

```swift
func add(num1 a: Int, num2 b: Int) -> Int {
    return a + b
}

let sumOfTwo = add(num1: 3, num2: 4)
```

添加**实参标签**后，并没有提高可读性，却使函数更长，没有太大必要，所以我认为这里不需要添加**实参标签**。

> 在Swift 2中，**实参标签**曾经称为**外部名**，**参数名**曾经称为**内部名**

### 参数默认为常量
Swift中，形参默认为常量，不可更改:
```swift
func addOneTo(_ num: Int) -> Int{
    num = num + 1 // 报错
    return num
}
```

上面代码错误，证明了形参的确是常量。下面是这段代码的报错信息：
> error: cannot assign to value: 'num' is a 'let' constant

## 深入理解循环
我们已经了解了基本的函数知识，下面我们通过这些知识，理解更多关于循环的问题。

设想我们处在这样一个世界，我们不知道加法如何运算，也就是说我们都不知道`a + b`等于多少。但是有一点我们知道，我们知道如何数数(1，2，3，4，5...)，也就是知道`a + 1`等于多少，也知道`a - 1`。换一种说法就是我们知道如果数到a，后面那个数字就是`a + 1`，前面那个数字就是`a - 1`。

### 基本问题描述
现在我们想要做一个加法运算。

我们把想要做的写为一个函数：
```swift
func add(_ a: Int, _ b: Int) -> Int {
    return a + b
}
```
但是在我们设想的这个世界，我们不知道如何计算`a + b`。怎么办？

### 我们知道什么
我们把我们所知道的`a + 1`写成一个递增函数：
```swift
func increase(_ num: Int) -> Int {
    return num + 1
}
```

我们把我们所知道的`a - 1`写成一个递减函数：
```swift
func decrease(_ num: Int) -> Int {
    return num - 1
}
```

### 用已知解释未知
那么现在，我们想一下怎么用递增和递减函数写出加法函数。

我们这样来看这个问题：
```swift
a + b = ((a - 1) + b) + 1
      = ((((a - 1) - 1) + b) + 1) + 1
      = ((((((a - 1) - 1) - 1) + b) + 1) + 1) + 1
      = (...(((b + 1) + 1) + 1) + ... + 1) + 1
      ...
```

我们将a减1（递减）的同时，整体加1（递增）。一直递减a，同时一直整体递增，直到a变为0。然后，变成了一个b递增的过程。b递增完成后，就可以得到结果了。这样，我们就将一个加法问题，变成了一个递增和递减的问题。

比如`4 + 3`可以写为：
```swift
4 + 3 = (3 + 3) + 1
      = ((2 + 3) + 1) + 1
      = (((1 + 3) + 1) + 1) + 1
      = ((4 + 1) + 1) + 1
      = (5 + 1) + 1
      = 6 + 1
      = 7
```

这样，如果用Swift写出来，就变成了：

```swift
func add1(_ a: Int, _ b: Int) -> Int {
    var num1 = a
    let num2 = b
    
    if num1 == 0 {
        return num2
    } else {
        num1 = decrease(num1)
        let s = add2(num1, num2)
        let result = increase(s)
        return result
    }
}
```

上面代码中，我们的`add()`函数中调用了自己（`add()`），这个过程叫做[递归](http://baike.baidu.com/link?url=QJGdti2urlLBjPeG91W84OC7WkSJCWCf3lAczFpy5KylJyFzIGF8c-H4iMAmfG3EA626KDuJTgjvR5Tq0F8LZJNsp-CdRNixFirLwNoZO43)。这里，暂时不对递归做深入分析，但是有一点要知道，递归比较费系统资源。

<br>

我们还可以这样来看这个问题：
```swift
a + b = (a + 1) + (b - 1)
      = ((a + 1) + 1) + ((b - 1) - 1)
      = (((a + 1) + 1) + 1) + (((b - 1) - 1) - 1)
      ...
```
当加号右边b递减的部分为0的时候，那么加号左边的部分就成为了我们需要的结果，是吗？这样，我们就将一个加法问题，变成了一个递增和递减的问题。

比如`4 + 3`可以写为：
```swift
4 + 3 = 5 + 2
      = 6 + 1
      = 7
```


这样，如果用Swift写出来，就变成了：

```swift
func add2(_ a: Int, _ b: Int) -> Int {
    var num1 = a
    var num2 = b
    
    if num2 == 0 {
        return num1
    } else {
        num1 = increase(num1)
        num2 = decrease(num2)
        
        let result = add(num1, num2)
        return result
    }

}
```

### 分析两种方法
我们再来分析一下上面两种递归方法，前一种我们记为**add1**，后一种**add2**。

首先分析**add1**，`4 + 3`为例：
```swift
        4 + 3	// add1(4, 3)，num1不为0，调用add1(3, 3)
      = (3 + 3) + 1		// add1(3, 3)，num1不为0，调用add1(2, 3)
      = ((2 + 3) + 1) + 1	// add1(2, 3)，num1不为0，调用add1(1, 3)
      = (((1 + 3) + 1) + 1) + 1		// add1(1, 3)，num1不为0，调用add1(0, 3)
      = ((((0 + 3) + 1) + 1) + 1) + 1		// add1(0, 3)，num1为0，返回num2 = 3
      = ((4 + 1) + 1) + 1	// 回到add1(1, 3)，返回3 + 1 = 4
      = (5 + 1) + 1		// 回到add1(2, 3)，返回4 + 1 = 5
      = 6 + 1		// 回到add1(3, 3)，返回5 + 1 = 6
      = 7		// 回到add1(4, 3)，返回6 + 1 = 7
```
根据上面分析，我们可以发现这里是一层层深入，再一层层返回的过程。

如果我们把横向考虑为空间，纵向认为是时间，那么可见时间上需要7步，空间上先增大后减小。在这个过程中，建立起一个延迟链，这里就是**延迟**递增操作。这个过程需要程序跟踪我们将要执行的操作，即存储一部分信息。以上操作中，我们**延迟**递增操作，不返回任何值，一直到**num1**变为0才开始返回。因此，需要存储的信息总量随着**a**的增大而线性增大，如下图所示：


```swift
      |   4 + 3	
      | = (3 + 3) + 1		
      | = ((2 + 3) + 1) + 1	
      | = (((1 + 3) + 1) + 1) + 1		
      | = ((((0 + 3) + 1) + 1) + 1) + 1		
      | = ((4 + 1) + 1) + 1	
      | = (5 + 1) + 1		
      | = 6 + 1		
      | = 7		
      |--------------------------------------> 空间
      ↓
    时间
```

<br>

然后分析add2，`4 + 3`为例：
```swift
	    4 + 3	// add2(4, 3)，num2不为0，返回add2(5, 2)
	  = 5 + 2	// add2(5, 2)，num2不为0，返回add2(6, 1)
	  = 6 + 1	// add2(6, 1)，num2不为0，返回add2(7, 0)
	  = 7 + 0	// add2(7, 0)，num2为0，返回num1
	  = 7
```

同样把横向考虑为空间，纵向认为是时间，那么可见时间上需要3步，空间上没有变化。在这个过程中，我们跟踪当前**num2**的值，最后返回**num1**的值。可以把**num2**看做一个指示器，**num1**看做一个寄存器。整个系统变化中，指示器以一定的规律变化，结果存储在寄存器中，可以根据指示器的信息判断出系统的状态。一旦指示器符合某种条件，那么系统停止运行，并返回寄存器的值，即为所需结果。


```swift
  |   4 + 3 
  | = 5 + 2
  | = 6 + 1
  | = 7 + 0
  | = 7
  |------------> 空间
  ↓
 时间
```

从上面的“时间-空间”图可以猜测出，后面一种方法占用的资源较小（时间✖️空间值较小），从而应该是更理想的一种实现方式。

<br>

**add1**和**add2**的区别可以理解为，我们能否由程序的参数确定系统的状态。
**add1**情况中，我们不能由输入的参数完全确定系统状态和运行位置，还需要一些隐藏的附加信息才可以。这些附加信息隐藏在我们刚才所说的延迟链中，延迟链越长，附加信息越多。这个过程叫做**递归过程**，实现的方法是**递归方法**。
**add2**情况中，我们可以由输入的参数完全确定系统状态和运行位置（我们输入参数包括指示器和寄存器）。如果我们在某一步停止运算，然后又想继续运行，所有我们需要做的就是把几个参数传给**add2**。这个过程叫做**迭代过程**，但是使用的是**递归方法**实现的。

综上所述，虽然两种方法都是**递归方法**，但是一个是**递归过程**，一个是**迭代过程**。大多数编程语言（C，C++，Java等）都不支持对采用**递归方法**的**迭代过程**的优化，一概都认为是**递归**，从而性能比较差。Swift稍微好点，但是也没有做到很好，所以呢，我们不能靠写出**迭代过程**的**递归方法**提高性能。

### 递归变为循环
那怎么办呢？我们可以通过循环来改写**迭代过程**的**递归方法**，改写**add2**如下：

```swift
func add3(_ a: Int, _ b:Int) -> Int {
    var num1 = a
    var num2 = b
    
    while num2 != 0 {
        num1 = increase(num1)
        num2 = decrease(num2)
    }
    
    return num1

}
```

回想**add2**，我们说过“可以把**num2**看做一个指示器，**num1**看做一个寄存器”，那么这里，我们就跟踪**num2**，最终返回**num1**即可。当**num2**不为0的时候，就进入循环体进行**num1**的递增和**num2**的递减。一旦**num2**为0，退出循环，返回**num1**。 注意到我们**add2**递归放在了方法末尾，所以这种形式的**递归过程**叫做**尾递归**（Tail Recursion）。在Swift中，**尾递归**可以通过改写为循环来提高性能。

这样，使用循环，就可以将性能提高。

## 求和
再以求和运算为例，看看递归和循环。

观察下面公式：

$$ sum=\sum _{n=k_{0}}^{k_{m}}n $$


可以写为：

$$ \begin{align*} & sum=k_{0}+\sum _{n=k_{1}}^{k_{m}}n\\ & 
=k_{0} + (k_{1} + \sum _{n=k_{2}}^{k_{m}}n) \\ & 
=k_{0} + (k_{1} + (k_{2} + \sum _{n=k_{3}}^{k_{m}}n)) \\ & 
=\ldots \\ & 
=k_{0} + (k_{1} + (k_{2} + \ldots +  (k_{m-1} + \sum _{n=k_{m}}^{k_{m}}n))) \\&
=k_{0} + (k_{1} + (k_{2} + \ldots +  (k_{m-1} + k_{m}))) \\&
\end{align*}
$$

这样一步步下来，我们可以看出，\\(k_{0}\\) 到\\(k_{m}\\)的和变成了从后到前先计算\\(s_{1}=(k_{m-1} + k_{m})\\)，再计算\\(s_{2}=(k_{m-2} + s_{1})\\)，一步一步向前计算，直到计算\\(sum=s_{m}=(k_{0} + s_{m-1})\\)

以上过程写成Swift代码如下：

```swift
func sum1From(_ from: Int, to: Int) -> Int {
    if from > to {
        return 0
    } else {
        return from + sumFrom(from + 1, to: to)
    }
}

let total = sumFrom(1, to: 100)
```

根据前面的讨论，这是一个**递归过程**的**递归方法**。下面我们将它改变为**迭代过程**的**递归方法**。

怎么样做呢？我们首先需要一个指示器，一个寄存器。这里，我们可以使用`from > to`作为指示器，然后引入一个专门的参数作为寄存器。可以按照以下思路计算：如果`from > to`不成立，那就将`from`递增，然后将`from`的原值加到寄存器。一旦`from > to`成立，那就返回寄存器的值。Swift代码如下：

```swift
func sum2From(_ from: Int, to: Int) -> Int {
    func iterateFrom(_ from: Int, result: Int) -> Int {
        if from > to {
            return result
        } else {
            return iterateFrom(from + 1, result: result + from)
        }
    }
    
    return iterateFrom(from, result: 0)
}
```

值得注意的是，我们在函数`sum2From(_:to:)`内又定义了一个函数`iterateFrom(_:result:)`，这在Swift中完全正确。然后我们返回`iterateFrom(_:result:)`的计算结果。

我们如果将这段代码转为循环呢：
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
这里，指示器还是`from > to`，寄存器是函数内定义的变量`result`。

上面的这些递归和循环的讨论，有没有一种将简单问题复杂化的感觉呢？那为什么还要这么做呢？未来，我们会遇到很多递归很好解决，但是不容易想出如何使用迭代，也就是循环的情况。如果你掌握了这里的技巧，那么计算效率将会有显著提高。

## 求解平方根
上面的例子我们是不是有一种感觉，Swift的函数就是数学里的方程？但是考虑下面这种情况：

$$ y=\sqrt {x},& 其中y /geq 0 而且y^{2}=x $$

怎么写为Swift函数？

上面的数学方程定义了什么是平方根，但是并没有任何如何求解的信息。但是如果要写一个Swift函数，必须要有怎么求解的信息。Swift函数和数学方程的区别在于前者描述做事的方法，后者描述这件事的性质。

那么我们到底应该如何计算呢？我们可以使用一种简化的**牛顿法**求解。这种方法首先需要一个初始猜测值，然后连续采取特定的操作改进这个初始值，直到满意为止。

如何把这种思路用在求平方根呢？我们可以将平方根的问题转变为：

$$ y^{2}=x $$

$$ y=\dfrac {x} {y} $$

可以想象，如果设置一个初始猜测值\\(y_{0}\\)，然后取\\(y_{0}\\)与\\(\dfrac {x} {y_{0}\\)的平均值，然后将此平均值作为新的猜测值代入函数，继续取平均值。连续采取此步骤，直到满意。

```swift
func loopSqrtOf(_ num: Float, guess: Float) -> Float {
    func isCloseEnough(_ num1: Float, _ num2: Float) -> Bool {
        if abs(num1 - num2) < 0.001 {
            return true
        } else {
            return false
        }
    }
    
    var myGuess = guess
    
    while !isCloseEnough(myGuess, num / myGuess) {
        myGuess = (myGuess + num / myGuess) / 2
    }
    
    return myGuess
}

loopSqrtOf(3, guess: 1)
```

由于以上代码已经是尾递归，我们很方便就可以将它转变为循环代码：
```swift
func sqrtOf(_ num: Float, guess: Float) -> Float {
    func isCloseEnough(_ num1: Float, _ num2: Float) -> Bool {
        if abs(num1 - num2) < 0.001 {
             return true
        } else {
            return false
        }
    }
    
    if isCloseEnough(guess, num / guess) {
        return guess
    } else {
        return sqrtOf(num, guess: (guess + num / guess) / 2)
    }
}

sqrtOf(3, guess: 1)
```


## 总结
1. 函数的基本知识
2. 递归，迭代，循环

## 下一步
更多控制流方法
