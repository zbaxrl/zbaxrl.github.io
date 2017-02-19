---
layout: post
title: NoneOJ1000 - 超大 Fibonacci
date: 2017-02-19 21:00:00
categories: OI
tags: [数论]
---

虽然明天要开学考，但是不想复习 `_(:з」∠)_`

## 题目来源
某次 QBXT 课上钟皓曦出的。

## 题目
> Fib(0) = 1
> Fib(1) = 1
> Fib(2) = Fib(1) + Fib(0) = 2
> Fib(n) = Fib(n-1) + Fib(n-2)
> 给定一个 k, 一个 c，求 Fib(c^k) mod P
> P = 1e9 + 7

## 做法
直接上数学结论。`Fib mod P` 是个循环群，循环群的大小叫做 Pisano period，它有个
好看的上界 `<= 6P`

