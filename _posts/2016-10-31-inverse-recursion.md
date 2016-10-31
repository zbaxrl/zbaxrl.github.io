---
layout: post
title: 数论 - 递推求逆元
date: 2016-10-31 17:49:00
categories: OI
tags: [数论, 逆元]
---

## 递推求逆元的推导

设 $$ t = M / i, k = M % i $$ ，那么有：

$$ it + k \equiv 0 \pmod M $$

$$ -it \equiv k \pmod M $$

两边同乘 $$ ik $$：

$$ -tk^{-1} \equiv i^{-1} \pmod M $$

带回：

$$ Inv[i] = (M - M/i) * Inv[M \% i] \% M $$

易知递推边界：

$$ Inv[1] = 1 $$


## 代码模板

```cpp
const int MOD = 1E9+7;
int Inv[100000];
void getInv(int c) {
    Inv[1] = 1;
    for(int i=2;i<=c;i++) Inv[i] = ((MOD - MOD/i) * Inv[MOD % i]) % MOD;
}
```

