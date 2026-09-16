---
title: CRT和exCRT
date: 2023-02-20 16:32:49
math: true
tags:
  - exCRT
  - CRT
categories:
  - [信息学,数学,数论,exCRT]
  - [信息学,数学,数论,CRT]
---

时间不多了，把数论复习一遍。

# [P1495 【模板】中国剩余定理（CRT）/ 曹冲养猪](https://www.luogu.com.cn/problem/P1495)

给定 $n$ 个整数 $m_1,m_2,\dots,m_n$ 保证它们**两两互质**，又给定任意 $n$ 个整数 $a_1,a_2,\dots,a_n$，现在要求一个整数 $x$，使其满足下面的线性同余方程组：
$$
\begin{cases}
x \equiv &a_1 \pmod{m_1}\\
x \equiv &a_2 \pmod{m_2}\\
& \vdots \\
x \equiv &a_n \pmod{m_n}
\end{cases}
$$

怎么操作捏？

我们记 $m_{total}$ 为 $\prod_{i=1}^n m_i$，$M_i$ 为 $\frac{m_{total}}{m_i}$，记 $M_i$ 关于 $m_i$ 逆元为 $t_i$，那么 $x$ 有一解为：
$$
x = \sum^n_{i=1} a_i M_i t_i
$$

当然这只是 $x$ 的一个特解，通解为：
$$
x'=x+km_{total} (k\in \mathbb{Z})
$$

这个特解构造为什么合理呢？因为如果只看 $a_iM_it_i$ 这一部分，对于 $m_1,m_2,\dots,m_{i-1},m_{i+1},\dots,m_n$ 它模出来都是 $0$。这样就做到了对于 $m_i$ 而言，除了 $a_iM_it_i$ 之外的运算都无效，进而使得 $x \equiv a_i \pmod{m_i}$ 。

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=20;
int a[N],m[N];
int n;
ll mT=1,M[N],t[N];

ll exgcd(ll a,ll b,ll &x,ll &y){
    if(b==0){
        x=1,y=0;
        return a;
    }
    ll d=exgcd(b,a%b,y,x);
    y-=(a/b)*x;
    return d;
}

ll inv(ll a,ll p){
    ll x,y;
    ll d=exgcd(a,p,x,y);
    x/=d;//有可能gcd算出来是-1，要让x回正
    return (x+p)%p;
}


signed main(){
    scanf("%d",&n);
    for(int i=1;i<=n;i++){
        scanf("%d%d",&m[i],&a[i]);
        m[i]=m[i]>0?m[i]:-m[i];
        a[i]=a[i]>0?a[i]:-a[i];
        mT=mT*m[i];
    }
    ll ans=0;
    for(int i=1;i<=n;i++) ans=(ans+a[i]*(mT/m[i])*inv(mT/m[i],m[i])%mT)%mT;
    printf("%lld\n",ans);
    return 0;
}
```

# [P4777 【模板】扩展中国剩余定理（EXCRT）](https://www.luogu.com.cn/problem/P4777)

这下 $m_1,m_2,\dots$ 不两两互质了，我们需要想想其他办法。

我们假设现在已经有一个 $x$，它已经满足了前 $k-1$ 个方程，现在想要满足第 $k$ 个。

我们记 $m'=\mathrm{lcm}(m_1,m_2,\dots,m_{k-1})$，显然前 $k-1$ 个方程的通解为 $x+tm' (t\in \mathbb{Z})$

于是我们需要一个 $t$，使 $x+tm'  \equiv a_k \pmod{m_k}$ ，即 $tm'  \equiv a_k-x \pmod{m_k}$

所以就用 `exgcd` 跑线性同余方程就行了

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef __int128 lll;
const int N=1e5+10;
int n;
ll m,a;
ll mT=1;//m'

ll exgcd(ll a,ll b,ll &x,ll &y){
    if(b==0){
        x=1,y=0;
        return a;
    }
    ll d=exgcd(b,a%b,y,x);
    y-=x*(a/b);
    return d;
}

int main(){
    scanf("%d",&n);
    ll ans=0;
    while(n--){
        scanf("%lld%lld",&m,&a);
        ll t,y;
        ll d=exgcd(mT,m,t,y);
        ll tmp=(a-ans)%m;
        tmp=tmp>=0?tmp:tmp+m;
        if(tmp%d){
            puts("HOW?");
            return 0;
        }
        ll raxio=tmp/d;
        t=(lll)t*raxio%(m/d);
        t=t>=0?t:t+(m/d);
        ans=(ans+t*mT);
        mT=mT/d*m;
        ans=ans%mT;
    }
    printf("%lld\n",ans);
    return 0;
}
```