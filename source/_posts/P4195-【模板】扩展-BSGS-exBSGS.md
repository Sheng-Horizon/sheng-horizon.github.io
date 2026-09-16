---
title: P4195 【模板】扩展 BSGS/exBSGS
date: 2022-08-19 22:29:58
tags: exBSGS
math: true
categories:
  - [信息学,数学,数论,exBSGS]
---


# 题目描述

给定 $a,p,b$，求满足 $a^x≡b \pmod p$ 的最小自然数 $x$ 。

## 输入格式

每个测试文件中包含若干组测试数据，保证 $\sum \sqrt p\le 5\times 10^6$。

每组数据中，每行包含 $3$ 个正整数 $a,p,b$ 。

当 $a=p=b=0$ 时，表示测试数据读入完全。

## 输出格式

对于每组数据，输出一行。

如果无解，输出 `No Solution`，否则输出最小自然数解。

## 样例 #1

### 样例输入 #1

```
5 58 33
2 4 3
0 0 0
```

### 样例输出 #1

```
9
No Solution
```

# 核心

$$
a^{x-k} \equiv \frac{b}{\prod^{k}_{i=1}g \times na}\pmod {\frac{p}{\prod^{k}_{i=1}g}} \\
其中，na=\prod^k_{i=1} \frac{a}{g_i},g=\gcd(a,p)
$$

# 优化点和注意点

1. 当原式 $a^x≡b \pmod p$ 中若 $b=1$ 或 $p=1$，$EXGCD$ 直接 ```return 0``` 即可

   因为若 $b=1$，$原式 \Rightarrow a^x \equiv 1 \pmod p$，显然当 $x=0$ 时成立且 $x$ 最小

   若 $p=1$，$a^{x} \mod 1=b\mod 1=0$，显然当 $x=0$ 时成立且 $x$ 最小

2. 当代码中 $p \nmid b$ 时，直接 ```return -1``` **（必须且重要）**

3. 当代码中 $na=b$ 时，直接 ```return k```

   **注意：**代码中的 $b$ 等于上式的 $\frac{b}{\prod^{k}_{i=1}g}$

   因为此时 $a^{x-k} \equiv \frac{b_{代码中}}{na} =1$，明显此时 $x-k=0$ 答案最佳，故 $x=k$。

4. 输入时 $a,b$ 要先模 $p$ **（非常必须且重要）**

# 代码

```cpp
#include<iostream>
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<ext/pb_ds/assoc_container.hpp>
#include<ext/pb_ds/hash_policy.hpp>
using namespace std;
using namespace __gnu_pbds;
typedef long long LL;
gp_hash_table<int,int> mp;
int a,b,p;
int ans;

template<typename T> inline void read(register T &x) {
    register int s = 1, d = getchar(); x = 0;
    for ( ;!isdigit(d); d = getchar()) if (!(d^'-')) s = -1;
    while (isdigit(d)) x = x * 10 + (d&15), d = getchar();
    x *= s;
}
template<typename T, typename...L> inline void read(register T &x, L &...l) { read(x), read(l...); }

template<class T> void write(register T x) {
	if (x < 0) { putchar('-'); x = -x; }
	if (x > 9) write(x / 10); putchar(x % 10 + '0');
}

inline int gcd(register int a,register int b){
    return b?gcd(b,a%b):a;
}

inline int exgcd(register int a,register int b,register int &x,register int &y){
    if(b==0){
        x=1,y=0;
        return a;
    }
    register int d=exgcd(b,a%b,x,y);
    register int z=x;
    x=y,y=z-(a/b)*y;
    return d;
}

inline int inv(register int a,register int p){
    register int x,y;
    register int d=exgcd(a,p,x,y);
    return (x/d%p+p)%p;
}

inline int qwqmi(register int a,register int b,register int mod){
    register int res=1;
    while(b){
        if(b&1) res=(LL)res*a%mod;
        a=(LL)a*a%mod;
        b>>=1;
    }
    return res;
}

inline int BSGS(register int a,register int b,register int p){
    mp.clear();
    b%=p;
    register int t=ceil(sqrt(p));
    register int val=b;
    for(int j=0;j<t;j++){
        mp[val]=j;
        val=(LL)val*a%p;
    }
    a=qwqmi(a,t,p);
    if(a==0) return b==0?1:-1;// Caution
    val=1;
    for(register int i=0;i<=t;i++){
        if(mp.find(val)!=mp.end()){
            register int j=mp[val];
            if(i*t-j>=0) return i*t-j;
        }
        val=(LL)val*a%p;
    }
    return -1;
}

inline int EXBSGS(register int a,register int b,register int p){
    if(b==1||p==1) return 0;//此时x可以肆意妄为(可以不要)
    register int g=gcd(a,p),k=0,na=1;
    while(g>1){
        while(p%g==0&&a%g==0){
            if(b%g) return -1;//不能整除无解！！！！！
            k++,b/=g,p/=g,na=(LL)na*(a/g)%p;
            if(na==b) return k;
        }
        g=gcd(a,p);
    }
    register int f=BSGS(a,(LL)b*inv(na,p)%p,p);
    return f==-1?-1:f+k;
}

int main(){
    // freopen("test.in","r",stdin);
    while(true){
        read(a,p,b);
        
        if(a==b&&b==p&&p==0) return 0;
        a%=p,b%=p;
        ans=EXBSGS(a,b,p);
        printf(ans==-1?"No Solution\n":"%d\n",ans);
    }
    return 0;
}
```



