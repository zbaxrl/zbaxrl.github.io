---
layout: post
title: SPOJ[QTREE] - Query on a tree
date: 2016-12-11 21:17:00
categories: OI
tags: [树链剖分, 线段树]
---

## 题目
给定一颗无向树，每次两种操作：

1. 求 $$ u -> v $$ 路径上边中的最大权值
2. 修改 $$ i $$ 条边的权值为 $$ T_i $$

非常裸的树链剖分求解树上统计。

诶为啥常数这么大！（580ms

自己写的代码好次啊。

## 代码
```cpp
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#define rep(_var, _start, _end) for(int _var=_start;_var<=_end;++_var)
#define per(_var, _start, _end) for(int _var=_start;_var>=_end;--_var)
#define out(_var, _cur) for(It _var=Head[_cur];_var;_var=_var->n)
#define CLR(_x) { memset(_x, 0, sizeof(_x)); }
using namespace std;
const int PSIZ = 2E6 + 1E3;
const int _oo  = 1E9;
int N;

struct tgno {
    int v, w, e;
    tgno *n;
} POOL[PSIZ]; int ppos;
typedef tgno* It;
It Head[PSIZ];

void _add(int u, int v, int w, int e) {
    POOL[ppos].v = v; POOL[ppos].w = w;
    POOL[ppos].e = e; POOL[ppos].n = Head[u];
    Head[u] = &POOL[ppos++];
}

void addEdge(int u, int v, int w, int e) { _add(u, v, w, e); _add(v, u, w, e); }

void input() {
    int u, v, w; int t = 1;
    rep(i, 2, N) { scanf("%d %d %d", &u, &v, &w); addEdge(u, v, w, t++); }
}

int dep[PSIZ], siz[PSIZ], fa[PSIZ], son[PSIZ], src[PSIZ], e2v[PSIZ];
int top[PSIZ], tid[PSIZ], trank[PSIZ], tpos;
void dfs1(int cur, int pre, int dd) {
    dep[cur] = dd, fa[cur] = pre, siz[cur] = 1;
    out(i, cur) {
        if(i->v == pre) continue;
        dfs1(i->v, cur, dd+1);
        siz[cur] += siz[i->v];
        if(!son[cur] || siz[i->v] > siz[son[cur]]) {
            son[cur] = i->v;
            src[i->v] = i->w;
            e2v[i->e] = i->v;
        }
    }
}

void dfs2(int cur, int tp) {
    top[cur] = tp, tid[cur] = ++tpos; trank[tid[cur]] = cur;
    if(!son[cur]) return;
    dfs2(son[cur], tp);
    out(i, cur) {
        if(i->v != son[cur] && i->v != fa[cur]) {
            src[i->v] = i->w;
            e2v[i->e] = i->v;
            dfs2(i->v, i->v);
        }
    }
}

struct zkwseg {
#define lson(x) ((x) << 1)
#define rson(x) (lson(x) | 1)
    int mmax[PSIZ<<2]; int cap, M;
    void mkTree(int c) {
        cap = c; for(M=1;M<cap;M<<=1) {} CLR(mmax);
        rep(i, 2, N) mmax[M-1+tid[i]] = src[i];
        per(i, M-1, 1) mmax[i] = max(mmax[lson(i)], mmax[rson(i)]);
    }
    void fix(int pos) {
        pos>>=1;
        for(;pos;pos>>=1) {
            mmax[pos] = max(mmax[lson(pos)], mmax[rson(pos)]);
        }
    }
    void change(int pos, int val) { pos += M-1; mmax[pos] = val; fix(pos); }
    int query(int s, int t) {
        int ans = -_oo;
        for(s=s+M-2,t=t+M;s^t^1;s>>=1,t>>=1) {
            if(~s & 1) ans = max(ans, mmax[s^1]);
            if( t & 1) ans = max(ans, mmax[t^1]);
        }
        return ans;
    }
} ST;

int querymx(int s, int t) {
    int ans = -_oo;
    while(top[s] != top[t]) {
        if(dep[top[s]] < dep[top[t]]) swap(s, t);
        ans = max(ans, ST.query(tid[top[s]], tid[s]));
        s = fa[top[s]];
    }
    if(tid[s] > tid[t]) swap(s, t);
    ans = max(ans, ST.query(tid[son[s]], tid[t]));
    return ans;
}

void change(int e, int w) {
    ST.change(tid[e2v[e]], w);
}

void work() {
    CLR(dep); CLR(siz); CLR(fa); CLR(son);
    CLR(top); CLR(tid); CLR(trank); tpos = 0;
    dfs1(1, 0, 0);
    dfs2(1, 1);
    ST.mkTree(N);

    char c[16]; int a, b;
    while(scanf("%s", &c) != EOF) {
        switch(c[0]) {
        case 'Q': { scanf("%d %d", &a, &b); printf("%d\n", querymx(a, b)); } break;
        case 'C': { scanf("%d %d", &a, &b); change(a, b); } break;
        case 'D': return;
        }
    }
}

int main() {
    int t; scanf("%d", &t);
    while(t--) {
        cin >> N;
        CLR(POOL); CLR(Head); ppos = 0;
        input();
        work();
    }
}
```

