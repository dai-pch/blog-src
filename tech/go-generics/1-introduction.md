---
title: "Go语言支持泛型啦"
# date: 2021-10-27T22:00:00+08:00
description: "Go语言的泛型终于要来啦🎉🎉🎉"
draft: true
tags: ["Golang", "Generics"]
categories: ["Software"]
---

Go语言的泛型终于要来啦🎉🎉🎉.

根据Go官方博客的[说明](https://go.dev/blog/generics-proposal)，Go语言的泛型（Generics）特性正在开发中，并将跟随2022年初的Go1.18版本发布。你可以在 https://go2goplay.golang.org/ 上预先体验Go语言的泛型支持。

Go语言缺乏泛型特性是我日常使用中的最大痛点，没有之一。
因为没有泛型，导致很多抽象无法实现——要么针对每个类型各自做一遍实现，要么使用`interface{}`做类型擦除。前者毫无疑问是个蠢方法，后者则完全丧失了类型系统约束和安全性。
现在Go语言终于要引入泛型了，有了泛型的Go终于达到了现代编程语言的最低水平了🐶🐶🐶。

根据官方说明，1.18版本将引入泛型特性，但暂时不会适配标准库。这意味着短期内slice、map等类型仍将保持原有实现，可能在1.19或未来版本再做修改。不过这不影响用户自定义类型的使用。

本文是对Go泛型提案（[proposal](https://go.googlesource.com/proposal/+/refs/heads/master/design/43651-type-parameters.md)）的简要记录和总结。
有时间和能力的同学，强烈建议阅读原版提案。提案内容非常详尽且易懂，有助于完整了解Go泛型的设计。

## 泛型参数

以一个简单的泛型函数为例。
假设我们需要一个函数可以在屏幕上打印任意类型的数组。
在没有泛型的情况下我们需要这么写

```
func Print(s []interface{}) {
    for _, v := range s {
        fmt.Println(v)
    }
}
```

然而如果我们用它打印一个整型数组

```
Print([]int{1, 2, 3})
```

你将看到一个编译错误。原因是Print函数接受一个"[]interface{}"类型的参数，但调用时传入的是一个"[]int"类型的值，而Go的智障编译器认为这两者是不同的类型。

有了泛型后，这个问题终于得以解决。我们可以使用一个泛型参数T表达任意类型

```
// 注意这个写法是不合法的
func Print(s []T) {
    // same as above
} 
```

上面这个写法是不合法的，原因在于Go的编译器不知道T这个名称的含义。
所以要想使用类型变量T，需要首先定义它。
在该提案中，对函数定义类型变量的方法是，给函数增加一个类型参数
（parameter，形参）列表，表示该函数接受一系列类型作为参数（argument，实参），就像接受一系列值作为参数列表的参数一样。

为了区别于正常的参数列表（以圆括号表示），类型参数用方括号[]包裹。因此合法的Print函数定义为

```
func Print[T any](s []T) {
    // same as above
}
```

其中，"[T any]"是类型参数列表，T是一个类型，any表示T可以是任何类型。

要调用泛型版本的Print函数，类型参数和值参数都要传入：

```
s := []int{1, 2, 3}
Print[int](s)
```

## 类型约束

再看一个例子

```
// This function is INVALID.
func Stringify[T any](s []T) (ret []string) {
	for _, v := range s {
		ret = append(ret, v.String()) // INVALID
	}
	return ret
}
```

Stringify函数将任意类型的数组转化为一个字符串数组。
转化是通过调用数组元素的String()方法实现的。
这里有一个问题，由于每个数组元素v的类型是T，而T可以是任意类型，因此v未必存在一个String()方法。
所以对v调用String()方法是不合法的。

为了解决这个问题，需要对T做约束。
所谓“类型约束”（Constrains），就是“类型的类型”，上面例子中any就是一个类型约束。
但它太过宽松了，无法要求T必须有一个`String() string`函数。

很自然的，我们想到使用interface作为类型T的类型约束。
我们可以定义一个interface

```
type Stringer interface {
    String() string
}
```

用Stringer作为泛型参数的约束，Stringify函数可以写作

```
func Stringify[T Stringer](s []T) (ret []string) {
	for _, v := range s {
		ret = append(ret, v.String())
	}
	return ret
}
```

类型约束用作两个用途。
一方面，它告诉编译器该类型可以使用的方法或操作符，以保证在函数内部可以调用这些方法。
另一方面，在泛型函数被调用时，编译器会检查泛型参数是否符合约束，以保证传入参数的合法性。

## 泛型类型



## 操作符和Type Set



## 一些看法
