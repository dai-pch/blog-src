---
title: "函数式编程笔记（三） —— 高阶函数与柯里化"
# date: 2021-10-27T22:00:00+08:00
description: "我学习函数式编程的记录和思考"
draft: true
tags: ["Functional Programming"]
categories: ["Software"]
---

## 高阶函数（High Order Function）

在本系列[第一篇文章]({{< relref "tech/functional-programming/1-features.md" >}})中提到过，“first class function”是函数式编程的本质特征之一。这意味着，一个函数的参数或返回值不仅可以是变量（数据），也可以是另一个函数。

```
func applyTwice(f func(int) int, x int) int {
    return f( f(x) )
}

func double(x int) int {
    return x * 2
}

applyTwice(double, 12) == 48
```

上面的伪代码是一个高阶函数的例子。
在代码中，double函数仅仅返回输入值的二倍；
applyTwice函数接受两个参数，第一个参数`f`是一个函数，第二个参数`x`是一个初始值，然后将`f`应用到`x`上两次，并返回结果。
将double函数和12作为参数传入到applyTwice函数中，结果就是12被执行了两次double，从而得到结果48。

在上面的例子中，applyTwice的第一个参数是一个函数，因此applyTwice是一个高阶函数。

## 柯里化（Currying）[^1]

我们再来看另一个例子。下面的代码是一个简单的add函数，它的返回值是两个参数之和。

```
func add(a int, b int) int {
    return a + b
}

add(3, 5) == 8
```

然而，有了高阶函数之后，我们可以写出这样的代码：

```
func add2(a int) func(int) int {
    return func(b int) int {
        return a + b
    }
}

add2(3)(5) == 8
```

这里的add2函数只有一个参数`a`，它的返回值是另一个函数，我们暂且叫做`add2_a`。
`add2_a`接受一个整数参数`b`，返回一个整数，其返回值是`a`与`b`的和。
在调用时，`add(2)`返回一个单参数函数；再用3作为参数调用该函数，即为`add(2)(3)`，其结果为两个参数的和8。

我们可以看到，从功能上，`add`和`add2`两个函数并没有本质的不同，但是`add2`是以高阶函数的形式表达的。
事实上，对于任意一个多参数函数`f`，我们都可以将它改写为类似的形式：即函数一次只接受一个参数，返回一个比原来函数少一个参数的函数。
这种转换称之为柯里化（Currying）。
柯里化对函数的功能不会有任何影响，但柯里化的函数允许我们部分应用（partially apply）一个函数，即只传给函数部分的参数。

我们可以再做一点推广。
考虑参数个数为零的函数，即无参数的函数。
无参数函数不接受任何参数，这意味着在纯函数的约束下，只要该函数被调用，就总会返回相同的值。
考虑到函数式编程中变量本身是不可变的，那么无参数函数和变量（常量）就没有任何区别了。
确实如此，在Scala中，无参数函数（用def定义）和不可变变量（用val定义）很大程度上是可以互换的。
而在Haskell这样的语言中，两者压根没有语法上的区别，从而实现了变量（数据）和函数（过程）的统一。

## Haskell的函数定义

为后文方便起见，这里稍微介绍一下Haskell中的函数定义。

```haskell
>> add x y = x + y

>> multiply x y = x * y

>> double x = multiply 2 x

>> double' = multiply 2

>> applyTwice f x = f ( f x )

>> three = 3

>> applyTwice double' three
12

```

上述代码是Haskell的交互式环境中函数定义和使用的几个例子。
函数定义通过`=`操作符表示，`=`的左边是函数名称及其参数，用空格分开；右边是函数体，也就是函数实际执行的运算和返回值。
在Haskell中，一切语句都是表达式，都会返回一个值。函数定义中，函数体表达式的值就是函数的返回值。
`add`和`multiply`的定义都是这种方式。

`double`函数有两个版本的定义：第一个版本是正常的函数定义，但是函数内部引用了另一个函数`multiply`；
另一个版本`double'`的功能与`double`完全一致，但是省略了参数`x`。
`double'`的定义为什么是合法的呢？
原因是，Haskell中的一切函数都是柯里化的，也就是可以部分应用的。
`multiply`是一个双参数函数，将一个参数2应用到`multiply`函数上，得到的值是另一个单参数函数。
`double'`的定义中，右边函数体的值正是这样一个单参数函数，该函数被绑定到`double'`这个名字上，从而完成了函数的定义。

最后一个例子是函数应用，含义是将`double'`和`three`作为参数传给`applyTwice`函数，不出意外的得到计算结果12。
由于Haskell中函数是完全柯里化的，参数应用的顺序也是从左到右，因此函数调用时参数不必加括号，只需空格分隔即可。

[^1]: “柯里化”（Currying）这个词来源于数理逻辑学家 Haskell Brooks Curry，编程语言Haskell也同样以他的名字命名。
