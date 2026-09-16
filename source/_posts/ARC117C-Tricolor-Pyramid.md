---
title: ARC117C-Tricolor Pyramid
date: 2023-02-11 09:58:32
tags: 构造
math: true
categories:
    - 信息学
    - 构造算法
---

非常神仙的构造题，写一篇来理解理解思路。

[洛谷 [ARC117C] Tricolor Pyramid](https://www.luogu.com.cn/problem/AT_arc117_c)

# 题目描述
一个金字塔由三种颜色 红、蓝、白 的方块组成。每个颜色块满足如下条件：
1. 如果一个颜色块下面两个颜色块颜色相同，那么该颜色块的颜色和下面两个颜色块颜色相同。
2. 如果一个颜色块下面两个颜色块颜色不同，那么该颜色块的颜色和下面两个颜色块颜色都不同。

    $Eg:$ 如果下面两个块颜色为 $R,W$，那上面那个颜色就是 $B$。
    
现在给定一个长度为 $N$ 的颜色条作为金字塔的最下层，问该金字塔最上面的颜色块的颜色。

其中，$2 \le N \le 400000$。

![eg](https://cdn.luogu.com.cn/upload/vjudge_pic/AT_arc117_c/f795a525a74e48b7e43010cb259b0e9d82262039.png)

首先，大规模数据处理的加速是可以想到多半和数学有点关系的。那既然和颜色有关系，我们不妨把三个颜色改写为 $0,1,2$。

接下来，两个块上面的块肯定得需要一个式子可以推得出来，就需要想一个式子。通常情况下，想式子有四个方向：**正负加减异或模**。

经过一同捣鼓可以发现点东西。比如说：

| |0|1|2|
|---|---|---|---|
|**0**|0|1|2|
|**1**|1|2|3|
|**2**|2|3|4|

上表为把 $0,1,2$ 加起来

| |0|1|2|
|---|---|---|---|
|**0**|0|1|2|
|**1**|1|2|0|
|**2**|2|0|1|

上表为把 $0,1,2$ 加起来再模 $3$。

| |0|1|2|
|---|---|---|---|
|**0**|0|2|1|
|**1**|2|1|0|
|**2**|1|0|2|

上表为把 $0,1,2$ 加起来，取负，再模 $3$。

然后一顿乱搞式子就出来了！设下方两个块的颜色分别为 $p_1,p_2$，上方颜色就是 $-(p_1+p_2) \mod 3$。

![题解](https://img.atcoder.jp/arc117/6d089e1cdfb0ed3c0bf6daee87fc198e.png)
![6](https://img.atcoder.jp/arc117/30f622287c540ae54b945356846aa0f9.png)

然后你又会发现，底座颜色对顶上的影响数是从底座到顶部的路径数。

这实际上是个什么？是个杨辉三角。

杨辉三角的每一个位置本质上是什么？对于杨辉三角位置 $(i,j)$，其值是 $\binom{i-1}{j-1}$

（为什么？因为推杨辉三角的式子是 $f[i][j]=f[i-1][j]+f[i-1][j+1]$,这个式子的形态和组合数那个基本上就是一样的）

然后金字塔最顶上那个块的颜色的式子就是：
$$
ans=(-1)^{N-1} \times \sum_{i=1}^N color_i \times \binom{N-1}{i-1} \mod 3
$$

用卢卡斯定理或者线性推一下就没了。

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=400400;
int n;
char s[N];
int color[N];

int f[N],g[N];//杨辉三角模3和答案

//由于模数 3实在是太小了，要特殊处理
int cnt3;//数3的个数

int main(){
    scanf("%d",&n);
    scanf("%s",s+1);
    for(int i=1;i<=n;i++){
        if(s[i]=='R') color[i]=0;
        else if(s[i]=='B') color[i]=1;
        else if(s[i]=='W') color[i]=2;
    }
    f[0]=g[0]=1;//C0,n-1
    for(int i=1;i<n;i++){
        int tmp=n-i;
        while(tmp%3==0) cnt3++,tmp/=3;//如果吧3乘进f里，后面取模就全是0了，开一个计数表示 3^cnt
        f[i]=(long long)f[i-1]*tmp%3;
        tmp=i;
        while(tmp%3==0) cnt3--,tmp/=3;
        f[i]=(long long)f[i]*tmp%3;//模3意义下，1和2的逆元等于自己
        if(cnt3) g[i]=0;
        else g[i]=f[i];
    }
    int ans=0;
    for(int i=1;i<=n;i++) ans=(ans+(long long)color[i]*g[i-1])%3;
    if((n-1)&1) ans=(-ans+3)%3;
    if(ans==0) puts("R");
    else if(ans==1) puts("B");
    else if(ans==2) puts("W");
    return 0;
}
```





