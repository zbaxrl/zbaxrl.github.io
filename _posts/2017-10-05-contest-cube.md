---
layout: post
title: NoneOJ1001 - 魔方 Cube
date: 2017-10-06 10:35:00
categories: OI
tags: [搜索, 置换群]
---

非常好的一道置换群搜索的题，见识到了。

题目来源是 QBXT 的一次模拟赛，出题人是吴克文。

## 题目
给一个 二阶魔方 的局面和最大搜索步数 N ，求字典序最小的还原方案的转动次数。

一共给出了 18 种单位转法：顶面顺时针 90 度，顶面顺时针 180 度，顶面顺时针 270 度，底面 90 度…

考虑到正对的两个面的旋转有旋转不变性，所以说实际转动的置换数只有 9 种，而且只有 3 种置换需要打表，其他的可以通过置换乘法得到。

妙啊。

重要的是思想，嘻嘻。

## 代码

```cpp
//#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <algorithm>
#include <functional>
using namespace std;

int N;

struct group {
    int C[25];
    
    group() {
        for(int i=1;i<=24;i++)
            C[i] = i;
    }
    
    group(const group &a) {
        memcpy(C, a.C, sizeof(C));
    }
    
    group(const int* sq) {
        for(int i=1;i<=24;i++)
            C[i] = sq[i];
    }
    
    friend group operator*(const group &a, const group &b) {
        group ret;
        
        for(int i=1;i<=24;i++)
            ret.C[i] = b.C[a.C[i]];
        
        return ret;
    }
    
    bool check() {
        for(int i=1;i<=24;i+=4) {
            if(C[i] != C[i+1] || C[i] != C[i+2] || C[i] != C[i+3])
                return false;
        }
        return true;
    }
};

group op[10];

void init() {
    int *ptr = op[1].C;
    ptr[1] = 3;
    ptr[2] = 1;
    ptr[3] = 4;
    ptr[4] = 2;
    ptr[10] = 6;
    ptr[6] = 14;
    ptr[14] = 23;
    ptr[23] = 10;
    ptr[9] = 5;
    ptr[5] = 13;
    ptr[13] = 24;
    ptr[24] = 9;
    op[2] = op[1] * op[1];
    op[3] = op[1] * op[2];
    
    ptr = op[4].C;
    ptr[9] = 11;
    ptr[10] = 9;
    ptr[12] = 10;
    ptr[11] = 12;
    ptr[1] = 21;
    ptr[21] = 17;
    ptr[17] = 5;
    ptr[5] = 1;
    ptr[3] = 23;
    ptr[23] = 19;
    ptr[19] = 7;
    ptr[7] = 3;
    op[5] = op[4] * op[4];
    op[6] = op[4] * op[5];
    
    ptr = op[7].C;
    ptr[5] = 7;
    ptr[6] = 5;
    ptr[8] = 6;
    ptr[7] = 8;
    ptr[4] = 10;
    ptr[10] = 17;
    ptr[17] = 15;
    ptr[15] = 4;
    ptr[3] = 12;
    ptr[12] = 18;
    ptr[18] = 13;
    ptr[13] = 3;
    op[8] = op[7] * op[7];
    op[9] = op[7] * op[8];
}

int cl[] = {0, 1, 1, 1, 2, 2, 2, 3, 3, 3};
group ss;

int history[47]; int hpos;
int dfs(group g, int cla, int cnt) {
    if((g*ss).check()) {
//      puts("!");
        return cnt;
    }
    if(cnt > N) return 0;
    
    int k;
    for(int i=1;i<=9;i++) {
        if(cl[i] != cla) {
            if((k = dfs(op[i] * g, cl[i], cnt+1))) {
                history[hpos++] = i;
                return k;
            }
        }
    }
    
    return 0;
}

int main() {
    freopen("cube.in", "r", stdin);
    freopen("cube.out", "w", stdout);
    
    init();
    scanf("%d", &N);
    
    group gg;
    for(int i=1;i<=24;i++) scanf("%d", &ss.C[i]);
    
    dfs(gg, 0, 1);
    
    for(int i=hpos-1;i>=0;i--) {
        printf("%d ", history[i]);
    }
    puts("");
}
```
