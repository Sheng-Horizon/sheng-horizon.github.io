---
title: 裴蜀定理和exgcd和线性同余方程
date: 2023-02-18 16:49:26
tags: 
    - 数论
    - 裴蜀定理
    - exgcd
categories:
    - [信息学,数学,数论,裴蜀定理]
    - [信息学,数学,数论,exgcd]
    - [信息学,数学,数论,线性同余方程]
math: true
---

数论本身是优美的，但是很多人选择用繁琐又不严谨的证明遮盖它的优美。

# 描述

《算法竞赛进阶指南》上对裴蜀定理的描述是错误的——真正的裴蜀定理应该这么描述：
> 对于任意两个整数 $a,b$，不定方程 $ax+by=c$ 有解 **当且仅当** $c$ 可以由 $\gcd(a,b)$ 整除。

# 证明

我们设 $\mathbf{A}= \{ax+by \vert x,y \in  \mathbb{Z} \}$。由于 $\mathbf{A}$ 非空且 $\mathbf{A} \in \mathbb{Z}$ 且 $\mathbb{Z}$ 的任意子集都存在最小值（最小值整到负无穷了除外）（或者说可以说是“[良序关系](https://zh.wikipedia.org/wiki/%E8%89%AF%E5%BA%8F%E5%85%B3%E7%B3%BB)”），因此 $\mathbf{A}$ 也存在最小值。

于是，我们只需要证明 $\mathbf{A}$ 的最小正元素为 $\gcd(a,b)$ 即可。

设  $\mathbf{A}$ 最小正元素为 $d_0=ax_0+by_0$。此时又考虑任意一个 $\mathbf{A}$ 中正元素 $p$，并对其进行对 $d_0$ 的带余除法——设 $p=qd_0+r$，其中 $q,r$ 为正整数，$0 \leq r < d_0$。

由此 
$$r=p-qd_0=(x_1-qx_0)a+(y_1-qy_0)b \in \mathbf{A}$$
而又因为 $0 \leq r < d_0$，$r$ 只能等于 $0$。

综上，对于任意一个 $\mathbf{A}$ 中正元素 $p$，$d_0 \vert p$。特别的，$d_0|a$ 且 $d_0|b$，因此 $d_0$ 为 $a,b$ 的公约数。

另一方面，对于 $a,b$ 的任意正公约数 $d$，设 $a=kd,b=ld$，那么
$$
d_0=x_0a+y_0b=(x_0k+y_0l)d
$$
因此 $d|d_0$，所以 $d_0$ 为 $a,b$ 的最大公约数。

综上，$\mathbf{A}$ 的最小正元素为 $\gcd(a,b)$，这意味着，对于不定方程 $ax+by=c$ 有解 都必须需要 $c$ 可以由 $\gcd(a,b)$ 整除。

综上，证毕。

# 例题 [P4549 【模板】裴蜀定理](https://www.luogu.com.cn/problem/P4549)

给定一个包含 $n$ 个元素的**整数**序列 $A$，记作 $A_1,A_2,A_3,...,A_n$。

求另一个包含 $n$ 个元素的待定**整数**序列 $X$，记 $S=\sum\limits_{i=1}^nA_i\times X_i$，使得 $S>0$ 且 $S$ 尽可能的小。

对于 $100\%$ 的数据，$1 \le n \le 20$，$|A_i| \le 10^5$，且 $A$ 序列不全为 $0$。

这道题用的并不是原版的裴蜀定理，而是对于多个元素的裴蜀定理扩展版。

即

> 对于不定方程 $\sum_{i=1}^{n} a_ix_i = c$ 有解当且仅当 $c$ 可以被 $\gcd(a_1,a_2,\dots,a_{n-1},a_n)$  整除。

那么这道题就对所有的 $a_i$ 取一个 $\gcd$ 就行了。

# exgcd（扩展欧几里得算法）

`exgcd` 就是用于求出一对 $x,y$，使得 $ax+by=\gcd(a,b)$ 的。

怎么求呢？
我们考虑参考 `gcd` 的运算的最后一步：$\gcd(a,b)$ 中 $b=0$

此时显然 $x=1,y=0$ 这一组解是成立的。（因为 $\gcd(a,0)=a$ 嘛！）

接下来考虑通常情况。

已知 $\gcd(a,b)=\gcd(b,a \mod b)$，于是我们可以得到：
$$
ax+by=\gcd(a,b)=\gcd(b,a \mod b)=bx'+(a \mod b)y'
$$

此时我们转化一下 $a\mod b$，把它写成 $a-\lfloor a/b\rfloor \cdot b$，代回原式：
$$
\begin{align}
ax+by &=bx'+(a-\lfloor a/b\rfloor \cdot b)y' \\
    &=ay'-b(x'-\lfloor a/b\rfloor y')
\end{align}
$$

于是，我们就可以得到
$$
\begin{cases}
x = y' \\
y = x'-\lfloor a/b\rfloor y'
\end{cases}
$$

于是递归求解就好了。

此时我们求得一组特解，怎么求通解呢？

我们考虑在 $x$ 上加上$\Delta x$，$y$上加上$\Delta y$

于是很显然的：$a \Delta x+b\Delta y=0$，并且 $\Delta x,\Delta y$ 必须是整数。

进一步的，$\Delta x=-\frac{b}{a} \Delta y$ 显然 $\Delta x=b,\Delta y=-a$ 是成立的。
由于 $\Delta x,\Delta y$ 之间的倍数不变，所以想让他们再变小一点就同时除以某个数就行了。而又因为需要他们为整数，所以就应该除以 $\gcd(a,b)$，这样新的 $\Delta x,\Delta y$ 没有了公约数，做到了最小。

因为 $\Delta x,\Delta y$ 做到了最小，他们的整数倍就能覆盖所有的通解。

所以，通解为：
$$
\begin{cases}
x'' = x+ k\frac{b}{\gcd(a,b)} \\
y'' = y-k\frac{a}{\gcd(a,b)}
\end{cases}
$$

非常完美

但是还有另一种特殊情况：方程为 $ax+by=c$，其中 $\gcd(a,b)| c $

其实按照上面的照样推一遍就行了：

特解：
$$
\begin{cases}
x''' = \frac{c}{\gcd(a,b)}x \\
y''' = \frac{c}{\gcd(a,b)}y
\end{cases}
$$

通解：
$$
\begin{cases}
x'' = \frac{c}{\gcd(a,b)}x+ k\frac{b}{\gcd(a,b)} \\
y'' = \frac{c}{\gcd(a,b)}y-k\frac{a}{\gcd(a,b)}
\end{cases}
$$

完结撒花

```cpp
int exgcd(int a,int b,int &x,int &y){
	if(b==0){
		x=1,y=0;
		return a;
	}
	int d=exgcd(b,a%b,y,x);//注意这里直接互换了
	y-=x*(a/b);
	return d;
}
```

# 线性同余方程

`exgcd` 可以用于解决线性同余方程。

假设我们有一个方程 $ax \equiv b \pmod{m}$
我们可以把它转化为 $ax+my=b$

然后套用 `exgcd` 就行了。
