---
layout: post
title: URAL1486 - Equal Squares
date: 2016-10-15 19:20:00
categories: OI
tags: [Hash, 字符串匹配]
---

上午看了一下冬令营论文《Hash函数在信息学竞赛中的一类应用》，主要讲这个增量哈希在字符串匹配上的应用。

然后例题就是这个辣！

主要解题思路是把一维的Rabin-Karp改成二维的。

$$ Hash(S_i) = \sum _{ i=1 }^{ len(S) }{ S[i] p^{len(S) - i} } \pmod q $$

$$ Hash(S_{i+1}) = pHash(S_i) + S[i] \pmod q $$

二维的不好用 LaTeX 写出来，所以说直接扔代码XDDDDD

```cpp
#include <iostream>
#include <algorithm>
using namespace std;
typedef unsigned long long ULL;
const int MAX_NM = 500 + 10;
const int SIZE   = MAX_NM * MAX_NM;
const ULL P1 = 101;
const ULL P2 = 999991;
const ULL Q1 = 45293;
const ULL Q2 = 37;   // STO - Generate by SageMath -  OTZ
int N, M; char Mat[MAX_NM][MAX_NM];
ULL hash1[MAX_NM][MAX_NM], powp1[MAX_NM], powq1[MAX_NM];
ULL hash2[MAX_NM][MAX_NM], powp2[MAX_NM], powq2[MAX_NM];
int x1, y1, x2, y2;
typedef pair< pair<int, int>, pair<int, int> > PII; 
PII hh[SIZE];
bool cmp(const PII &a, const PII &b) {
    if(a.first != b.first) return a.first < b.first;
    else return a.second < b.second;
}

bool check(int k) {
    int tot = 0; ULL h1, h2;
    for(int i=k;i<=N;i++) {
        for(int j=k;j<=M;j++) {
            h1 = hash1[i][j] - hash1[i][j-k] * powp1[k] - hash1[i-k][j] * powq1[k] + hash1[i-k][j-k] * powq1[k] * powp1[k];
            h1 = hash2[i][j] - hash2[i][j-k] * powp2[k] - hash2[i-k][j] * powq2[k] + hash2[i-k][j-k] * powq2[k] * powp2[k];
            ++tot;
            hh[tot].first = make_pair(h1, h2);
            hh[tot].second = make_pair(i - k + 1, j - k + 1);
        }
    }

    sort(hh + 1, hh + 1 + tot, cmp);
    
    for(int i=1;i<tot;i++) {
        if(hh[i].first == hh[i+1].first) {
            int isans = 1;
            for(int x=0;x<k;x++) {
                for(int y=0;y<k;y++) {
                    if(Mat[x+hh[i].second.first][y+hh[i].second.second] != Mat[x+hh[i+1].second.first][y+hh[i+1].second.second]) {
                        isans = 0; goto _end;
                    }
                }
            }

            _end: ;
            
            if(isans) {
                x1 = hh[i].second.first; y1 = hh[i].second.second;
                x2 = hh[i+1].second.first; y2 = hh[i+1].second.second;
                return true;
            }
        }
    }

    return false;
}

int binary(int L, int R) {
    while(L != R) {
        int Mid = (L + 1 + R) >> 1;
        if(check(Mid)) L = Mid;
        else R = Mid - 1;
    }
    if(check(L))
        return L;
    else for(int i=1;i<=L;i++) if(check(L - i)) return L - i;
    return L;
}

int main() {
    ios::sync_with_stdio(false);
    cin >> N >> M;
   
    // 预处理各种pow值
    powp1[0] = powq1[0] = powp2[0] = powq2[0] = 1;
    for(int i=1;i<=M;i++) powp1[i] = powp1[i-1] * P1;
    for(int i=1;i<=N;i++) powq1[i] = powq1[i-1] * Q1;
    for(int i=1;i<=M;i++) powp2[i] = powp2[i-1] * P2;
    for(int i=1;i<=N;i++) powq2[i] = powq2[i-1] * Q2;

    char c;
    for(int i=1;i<=N;i++) {
        for(int j=1;j<=M;j++) {
            cin >> c; Mat[i][j] = c - 'a';
            hash1[i][j] = Mat[i][j] + hash1[i][j-1] * P1 + hash1[i-1][j] * Q1 - hash1[i-1][j-1] * P1 * Q1;
            hash2[i][j] = Mat[i][j] + hash2[i][j-1] * P2 + hash2[i-1][j] * Q2 - hash2[i-1][j-1] * P2 * Q2;
        }
    }

    int ans = binary(0, min(N, M));
    cout << ans << endl;
    if(ans) {
        cout << x1 << " " << y1 << endl
             << x2 << " " << y2 << endl;
    }
}
```

事实上这个代码很玄学，质数是拿 SageMath 随机出来的，交了WA掉好几次，原因是这个 Hash 有一定概率碰撞（X）

所以说检测到相同的hash之后暴力跑一遍cmp是坠吼的。。
