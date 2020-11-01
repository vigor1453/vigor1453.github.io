---
layout: post
title: "树状数组"
date: 2019-08-24
tags: ["旧","技术"]
comments: true
author: oneman233
excerpt: "把一维的数列拉到二维去"
toc: true
---

它有一个非常重要的性质：

$$tr[ans]=\sum_{i=ans-lowbit(ans)+1}^{ans} \ a_i$$

其中$lowbit(x)$是$x$的二进制最低位$1$的位置所对应的值。

如果：$ans=2^k$，那么：

$$lowbit(ans)=ans$$

此时：

$$tr[ans]=tr[2^k]=a[1]+a[2]+...+a[2^k]$$

这条性质在求解全局第k大时会用到。

# 统计前缀和，支持单点修改

基于$lowbit(x)=x\&(-x)$实现，树状数组定义为$tr[maxn]$。

```c++
int sum(int x) {
    res=0;
    for(int i=x;i>0;i-=lowbit(i))
        res+=tr[i];
    return res;
}//查询[1,x]区间的前缀和
void add(int x,int pos) {
    for(int i=pos;i<=maxn;i+=lowbit(i))
        tr[i]+=x;
}//在pos位置上加x
```

# 查询全局第k大

需要把树状数组建立在值域上，即$tr[i]$储存的是值为$i$的值有多少个。

只需要在建立树状数组时如下处理：

```c++
for(int i=1,x;i<=maxn;++i)
    cin>>x,add(x,1);
```

注意值域过大时候需要离散化。

求全局第$k$大的代码：

```c++
/*
    ans保存的是第k大值
    cnt则保存了[1,ans]区间当中树状数组的总和
*/
int kth(int k) {
    int ans=0,cnt=0;
    for(int i=20;i>=0;--i) {//20的大小可以根据值域进行调整
        ans+=(1<<i);
        if(ans>maxn||cnt+tr[ans]>=k)
            ans-=(1<<i);
        else
            cnt+=tr[ans];
    }
    return ans+1;//返回值是第k大值
}
```

事实上是一种倍增的思想，要把$cnt$控制在刚好小于$k$的位置，$ans$则保存了这个刚好小于$k$的位置。

实际上求解全局第$k$大有另一种方法：

    用类似于快排的思想，选取一个支点，大于它的放到右边，否则放左边。
    如果恰好是第k个则return，否则左边数字个数大于k则去左边找，反之去右边。

# 一个方便的离散化和去重写法

```c++
int tot=0;
int a[maxn],b[maxn],c[maxn];
for(int i=1;i<=n;++i)
    cin>>a[++tot];
b[tot]=a[tot];
sort(a+1,a+1+n);
tot=unique(a+1,a+1+n)-a-1;
for(int i=1;i<=n;++i) {
    int k=lower_bound(a+1,a+1+tot,b[i])-a;
    c[k]=b[i];
    b[i]=k;
}
/*
    处理后a已经失去作用
    b存储的是某个数的排名
    c储存的是这个排名的数字原本是多少
*/
```

# 求解逆序对

每次读入数字$a[i]$，就调用$add(a[i],1)$，对答案的贡献是$sum(a[i]-1)$，也即比它小的数字个数。

当然归并也可以做。