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
现在Go语言终于要引入泛型了，这将大大提升Go的语言表达能力。

根据官方说明，1.18版本将引入泛型特性，但暂时不会适配标准库。这意味着短期内slice、map等类型仍将保持原有实现，可能在1.19或未来版本再做修改。不过这不影响用户自定义类型的使用。

本文是对Go泛型提案（[proposal](https://go.googlesource.com/proposal/+/refs/heads/master/design/43651-type-parameters.md)）的简要记录和总结。
有时间和能力的同学，强烈建议阅读原版提案。提案内容非常详尽且易懂，有助于完整了解Go泛型的设计。

