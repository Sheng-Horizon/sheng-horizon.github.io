---
title: KMP
date: 2023-02-27 17:52:45
math: true
tags:
  - KMP
  - 字符串
categories:
  - [信息学,字符串,KMP]
---

太多要复习了，脑阔痛。
复习半天双向搜索，还是来复习点简单的缓缓

整个 `KMP` 唯一难搞的地方就是 `ne[]`。其意义如下：
1. `ne[i]` 表示当模式串和主串在模式串位置 $i$ 处匹配发生失配时，模式串的下标应该会退到的位置。
2. `ne[i]` 也可以表示在模式串截止到位置 $i$ 的前缀子串中，从头开始和从末尾开始的最长相同子串的长度。

如图:
![](https://cdn.luogu.com.cn/upload/image_hosting/ozrlvirm.png)

长的是主串，短的是模式串。当主串与模式串在 $i$ 处发生失配时，相比从头开始重新匹配，只移到 `ne[i]` 处就使复杂度得到了保障。

时间复杂度 $O(n+m)$。

``` cpp
#include<bits/stdc++.h>
using namespace std;
const int N=1e6+10;
char a[N],b[N];//a是主串，b是模式串
int ne[N];
int n,m;

int main(){
    scanf("%s%s",a+1,b+1);
    int n=strlen(a+1),m=strlen(b+1);
    for(int i=2,j=0;i<=m;i++){//i从2开始是因为避免处理出来ne[1]=1，这样就卡死循环了
        while(j&&b[i]!=b[j+1]) j=ne[j];
        if(b[i]==b[j+1]) j++;
        ne[i]=j;
    }
    for(int i=1,j=0;i<=n;i++){
        while(j&&a[i]!=b[j+1]) j=ne[j];
        if(a[i]==b[j+1]) j++;
        if(j==m){
            printf("%d\n",i-j+1);
            j=ne[j];
        }
    }
    for(int i=1;i<=m;i++){
        printf("%d ",ne[i]);
    }
    return 0;
}
```

