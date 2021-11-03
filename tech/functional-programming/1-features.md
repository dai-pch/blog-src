---
title: "函数式编程笔记（一） —— 函数式编程的基本特征"
date: 2021-10-27T22:00:00+08:00
description: "我学习函数式编程的记录和思考"
draft: false
tags: ["Functional Programming"]
categories: ["Software"]
---

## 前言
本系列笔记是我学习函数式编程过程中的一些记录和思考，也作为与他人交流讨论的基础。

本系列假定读者有中等以上软件系统的开发经验，对面向对象编程的基本概念有一定了解。不要求读者对函数式编程有实际了解。

本系列看作者心情不定期更新。

## 什么是函数式编程

函数式编程是软件开发的著名范式之一。作为与“结构化编程”、“面向对象编程”等并列的编程范式，函数式编程本身的内涵是非常丰富的，包括函数式的语法特性、设计模式、抽象方法等一系列内容。

编程范式的具体概念通常没有一个清晰的界定，一是因为编程范式本身也在实践中不断发展变化，总有新的概念和方法被加入进去，也有老的部分被剔除；二是因为不同人和语言对范式概念和最佳实践的理解不同。
举例来说，C++、Java和Smalltalk都是典型的支持面向对象范式的语言，但它们也有微妙的不同：
Smalltalk中的类（Class）自身也是一个对象，但C++和Java中对类和对象概念则有明确的区分；
C++在对象的封装上开了洞，允许友元（friend class/function）访问对象的内部数据，但Java却无此特性；
Java中有接口（interface）的概念，但C++中没有，同时C++允许类的多重继承，但Java却不允许；等等。

虽然一种编程范式的边界难以区分，但通常其共性部分居多，这些共性部分通常表现为语言特性（比如面向对象中的封装、多态等概念）、设计模式甚至架构思路。
我们可以通过学习一个纯粹函数式编程语言来了解函数式编程的基本思想（就像通过学习Java来了解面向对象编程）。

本系列文章将以Haskell为主要参考语言，介绍函数式编程的基本思路和方法，也可能部分参考或对比Go、Scala等其他语言。

## 为什么要学习函数式编程

俗话说“当你手里有一把锤子时，你看什么都像钉子；当你手里有一把螺丝刀，你看什么都像螺丝”。工具箱越丰富，面对问题时就越容易快速找到合适的方法。
函数式编程是一个强大的工具，提供了独立于面向对象的另一种抽象方法，用它来丰富自己的工具箱最合适不过。

值得指出的是，函数式编程与面向对象编程并非两种完全独立的编程范式，它们也并不是互不相容的。
许多编程语言同时支持这两种编程范式，典型的比如Scala和OCaml；
另外许多现代编程语言，如Python，C++，Go，Rust等都或多或少的吸收了函数式编程的某些特性。
即使是Java这样的纯面向对象语言，也加入了lambda函数这种来自于函数式编程的特性。

在大型软件系统里，同时使用这两种范式做不同层次的抽象，不仅是可能的，而且是有益的。著名的前端框架React就是一个典型的函数式编程实践。
根据我个人的经验，面向对象范式适合于大型系统的分块和边界划分，而函数式编程适合于局部复杂运算的抽象和复用。

## 函数式编程的常见特征

参考Haskell和其他的函数式编程语言，我们可以总结出一些函数式编程的基本特性，这可以作为对函数编程的初步认识。
这些特性包括：

- 不可变性（immutable）

不可变性是指，一个变量除了在定义时被赋初始值，之后的整个生命周期其值保持不变。
从另一个角度理解，纯函数编程中不存在赋值操作。
不可变性的存在使得变量（variable）和常量（constant）再无差别，因此在Haskell这样的语言中，没有常量的概念。

在不可变的情况下，一个计算（函数）不能改变变量的值，只能通过返回一个新的变量来表示运算的结果。
例如，append函数向某数组追加一个元素，它不能改变该数组的值，只能返回一个新的数组表示追加后的结果。

不可变性对软件编程带来了很多好处，比如我们不需要担心一个结构体被传入其他函数后遭到改变，从而大大减小了心智负担和debug的复杂度；
不可变性在大规模并行计算中也有巨大的好处，因为它消除了共享内存和锁的问题。

不可变性也引入了一些代价，例如对状态的处理更加麻烦，以及对运行效率的担心等。
但也有很多相应的方法和技术解决这些问题。
总的来说，我认为除了某些特殊领域，引入不可变性是利大于弊的。

- 函数是一等公民（first class function）

这一特性是指，函数在编程语言中是一种基本的元素。尤其是，函数可以和数据（变量）一样作为另一函数的参数或返回值。

如果一个函数f以另一个函数g作为参数或返回值，那么f通常被称为“高阶函数”。
后面我们将看到，高阶函数是函数式编程中大部分抽象的基础，高阶函数的应用是函数式编程的精髓所在。

稍作延伸，函数式编程中函数和数据的界限是模糊的。函数可以作为数据出现、数据也可以当做函数（这里有个精妙的例子，但地方太小我写不下:P）。
更广义的说，在面向过程编程中，过程（代表计算）和数据的区分也并非天然的。
或许计算这件事，从本质上就与数据并无分别吧。

- 惰性计算（lazy evaluation）

惰性计算是指，程序只在必要的时候才会被计算。
比如程序中存在一次函数调用，在运算时并不会立即执行被调用函数，而是延迟到真正需要该调用的返回结果时，才去执行并得到结果。
又比如，一个函数返回一个数组，并非数组中所有值都会被计算，只有那些被用到的值才会真正被计算出来。

惰性计算带来了一些有趣的特性，比如我们可以在程序中构造和使用无穷长的数组，这一点会在后续文章中做详细介绍。

惰性计算是否是一个函数式编程语言必须的特性，这一点有待讨论。在Haskell中计算默认是惰性的，但其他许多函数式语言如Lisp、OCaml、Scala等并不是默认惰性计算的。
不过关于惰性计算，有两个定理可供参考：
一是，如果不可变性（immutable）严格成立，那么正则序求值和规范序求值结果完全相同。翻译成人话就是，在不可变性的情况下，Lazy与否不影响最终计算结果。
二是，在严格求值（立即求值）模型中，可以写出惰性求值的程序；在惰性求值的模型中，也可以写出立即求值的程序。

- 纯函数（pure function）

一个函数被称为纯函数，如果满足下列条件：
    1. 在任意情况下多次计算，只要参数相同，那么得到的结果都相同。
    2. 函数运行不会造成函数以外环境的任何改变和影响（无副作用）。

显然，这里说的“函数”是指编程语言中的函数，并非数学上的函数概念。这里的“纯函数”才是数学上函数概念的对应。
直观的理解，上述条件1的含义是，函数不依赖于非局部变量、静态变量，参数没有可变引用、输入等。
条件2的直观含义是，函数不改变非局部变量、静态变量、可变引用参数，不造成相应输出（控制台、网络、磁盘）等。这类影响也被称为副作用（side effect）。

纯函数对于程序实现、debug、测试等都是极为方便的。
但是我们知道，一个程序如果完全没有副作用，那么这个程序是无用的。这意味着它无法接受任何输入，也无法产生任何输出、对外部世界造成影响。
所以函数式编程也需要一些手段能够产生副作用。

在Haskell中，副作用和纯函数是严格分离的。程序的主体应该是纯函数，副作用被隔离为一个极小的模块，只负责与外界的交互。

### 其他常见特性
还有一些常见特性，并非函数式编程的核心和必须部分，但通常它们的出现和使用使得函数式编程更为方便，因此列举如下：

- 代数数据类型（algebraic data type）

代数数据类型是一种从现有类型组合出新数据类型的方法，包括“和类型”和“积类型”两种。具体内容将在后文介绍。

- 模式匹配（pattern matching）

模式匹配是一种语法糖，本质上它和过程式编程中的switch/if语句类似。但可以用来方便的处理代数数据类型。

- 类型系统和类型推导

虽然不是必须，但现代函数式编程语言通常都有极为强大的静态类型系统（比如Haskell、Scala、OCaml都有很完善的类型系统），类型系统对提高函数式程序的可读性、可靠性都有很强的帮助。
同时这类语言还会配备强大的类型推导算法，在保证类型系统的功能情况下，大大减少了程序员写类型注解的麻烦，提高了编程效率。

类型系统将在后文专门介绍。

## 最后

过程式编程和面向对象编程，其基本计算模型是图灵机；而函数式编程则完全不同，其理论模型基于λ-演算（λ-calculus）。
二者在运算模型上的根本不同，是许多特性和思路差异的来源。这一点本身也是一个有趣的话题，有机会再讨论这部分内容。

本文主要列举了函数式编程的一些基本特性，作为对函数式编程的直观印象，后文将继续介绍函数式编程的更多内容。

如果你对本文的任何内容有疑问或不同意见，欢迎留言讨论。
