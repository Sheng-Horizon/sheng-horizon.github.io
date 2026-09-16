---
title: ST表（倍增表）
date: 2023-02-28 17:35:04
tags: 
    - 信息学
    - ST表
categories:
    - 信息学
    - C++基础
    - ST表
---

看上次做ST表示整整一年前啊，当时刚初三下期。

高中确实没有初中快乐，更少的朋友，更多的压力。

害，往事不堪回首。

ST表就是倍增思想，和倍增求 `LCA` 非常像。具体看一看代码就行了

[P3865 【模板】ST 表](https://www.luogu.com.cn/problem/P3865)

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=1e5+10;
int n,m;
int a[N];
int MAX[N][20];

void init(){
    for(int j=0;j<19;j++){
        for(int i=1;i<=n&&i+(1<<(j-1))<N;i++){
            if(j==0) MAX[i][0]=a[i];
            else MAX[i][j]=max(MAX[i][j-1],MAX[i+(1<<(j-1))][j-1]);
        }
    }
}

int query(int l,int r){
    int len=r-l+1;
    int logg=0,tmp=len>>1;
    while(tmp) logg++,tmp>>=1;
    return max(MAX[l][logg],MAX[r-(1<<logg)+1][logg]);
}

int main(){
    freopen("test.in","r",stdin);
    scanf("%d%d",&n,&m);
    for(int i=1;i<=n;i++) scanf("%d",&a[i]);
    init();
    for(int i=1;i<=m;i++){
        int l,r;
        scanf("%d%d",&l,&r);
        printf("%d\n",query(l,r));
    }
    return 0;
}
```


