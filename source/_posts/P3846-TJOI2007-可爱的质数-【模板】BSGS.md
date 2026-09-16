---
title: P3846 [TJOI2007] 可爱的质数/【模板】BSGS
date: 2022-08-19 17:29:35
tags: BSGS
math: true
categories:
  - [信息学,数学,数论,BSGS]
---

# 应用场景

给定整数 $a,b,p$，其中 $a,p$ 互质，求一个非负整数 $x$，使得 $a^x\equiv b (\mod p)$

# 核心

设 $x=i*t-j$，其中 $t=\lceil\sqrt{p}\rceil$，$0\le j\le t-1$，则方程变为 $a^{i*t-j}\equiv b(\mod p)$。即 $(a^t)^i\equiv b*a^j$。

# 优化点和注意点

1. $BSGS$ 中计算完 $a^k$ 后，若 $a^k=0$，则
   $$
   \begin{cases}
   \text{return 1} & b=0\\
   \text{return -1} &b\neq0
   \end{cases}
   $$
   

# 代码

```cpp
#include<iostream>
#include<cstring>
#include<cstdio>
#include<algorithm>
#include<ext/pb_ds/assoc_container.hpp>
#include<ext/pb_ds/hash_policy.hpp>
using namespace std;
using namespace __gnu_pbds;
typedef long long LL;
gp_hash_table<LL,LL> mp;
LL a,b,p;
LL ans;

LL qwqmi(LL a,LL b,LL p){
    LL res=1;
    while(b){
        if(b&1) res=res*a%p;
        a=a*a%p;
        b>>=1;
    }
    return res;
}

LL BSGS(LL a,LL b,LL p){
    mp.clear();
    b%=p;
    LL t=ceil(sqrt(p));
    LL val=b;
    for(LL j=0;j<t;j++){
        mp[val]=j;
        val=val*a%p;        
    }
    a=qwqmi(a,t,p);
    if(a==0) return b==0?1:-1;
    val=1;
    for(LL i=0;i<=t;i++){
        if(mp.find(val)!=mp.end()){
            LL j=mp[val];
            if(i*t-j>=0) return i*t-j;
        }
        val=val*a%p;
    }
    return -1;
}

int main(){
    scanf("%lld%lld%lld",&p,&a,&b);
    ans=BSGS(a,b,p);
    printf(ans==-1?"no solution":"%lld\n",ans);
    return 0;
}
```

