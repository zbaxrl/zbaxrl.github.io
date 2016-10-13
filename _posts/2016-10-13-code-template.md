---
layout: post
title: 代码模板
date: 2016-10-13 19:52:00
categories: 杂
tags: OI
---

放一个刷题用的代码模板

```cpp
#include <bits/stdc++.h>
using namespace std;
#define lowbit(x) (x)&(-x)
#define lson(x) ((x)<<1)
#define rson(x) (lson(x)+1)
#ifdef LCYTEST
#define test(x) {x}
#else
#define test(x) {;}
#endif

int main(){
}
```
