---
layout: post
title: "逆元、exgcd以及威尔逊定理等等"
date: 2019-08-24
tags: ["旧","算法"]
comments: true
author: oneman233
excerpt: "(a/b)%c以及ax+by=c"
toc: true
---

# 逆元相关

取余有很多好性质：

$$
(a + b)\%c=(a\%c + b\%c)\%c
\\
(a * b)\%c=(a\%c * b\%c)\%c
$$

但是对于$(a/b)\%p$，以上性质不能使用，只能用下面这条式子：

$$(a / b)\%p=(a * b^{-1})\%p$$

上式中$b^{-1}$称为$b$在$mod \  p$意义下的逆元。

逆元有这样的性质：

$$b*b^{-1}\equiv 1(mod \ p)$$

逆元的求解方法主要有三种：

## 费马小定理加快速幂

**费马小定理：**

如果$p$是一个质数，而整数$a$不是$p$的倍数，则有$a^{p-1}\equiv 1(mod \ p)$。

注意只适用于$p$是质数且$a$不是$p$的倍数的情况：

$$b^{-1}(mod \ p)\equiv b^{p-2}(mod \ p)$$

## exgcd

```c++
void exgcd(int a,int b,int &x,int &y) {
    if(!b) x=1,y=0;
    else exgcd(b,a%b,y,x),y-=a/b*x;
    int x,y;
    exgcd(a,p,x,y);
    x=(x%p+p)%p;
}
```

此时$x$是$a$在模$p$意义下的逆元，要求$a$与$p$互质，$p$可以不为质数。

为了证明上述过程的正确性，先介绍$exgcd$算法：

已知整数$a$、$b$，扩展欧几里得算法可以在求得$a$、$b$的最大公约数的同时，找到整数$x$、$y$（其中一个很可能是负数），使它们满足贝祖等式：$ax + by = gcd(a, b)$。

**贝祖定理：**

对于给定的正整数$a$、$b$，方程$a*x+b*y=c$有解的充要条件为$c$是$gcd(a，b)$的整数倍。

实际上观察：

$$a*x\equiv 1(mod \ p)$$

可以化成：

$$a*x-p*y\equiv 1(mod \ p)$$

不妨把上式中的减号改成加号，得到：

$$a*x+p*y\equiv 1(mod \ p)$$

由于$a$与$p$互质，即$gcd(a, b)=1$，套用$exgcd$即可。

**这里有两个有意思的结论：**

1. 如果$x$、$y$互质，那么不可用$x*a+y*b$表示的最大正整数为$x*y-x-y$，其中$a$、$b$都为自然数。
2. 当 $a_i$ 和 $x_i$ 均为整数时, $a_1x_1+a_2x_2+…+a_nx_n$的值一定为$ gcd(a_1,a_2,…,a_n) $的整数倍。

在[这道题](https://codeforces.com/problemset/problem/1010/C)中会用到。

## 线性递推

有时候可能需要预处理一大堆数据在模$p$意义下的逆元，这时候需要用到逆元的线性递推式。

```c++
inv[1]=1;
inv[i]=(p-p/i)*inv[p%i]%p;
```

简单证明如下：

首先：

$$1^{-1}\equiv 1(mod \ p)$$

设：

$$p=k*i+r$$

即：

$$k=p/i,r=p\%i$$

则：

$$k*i+r\equiv 0(mod \ p)$$

上式两边同时乘以：

$$i^{-1}*r^{-1}$$

得到：

$$k*i*i^{-1}*r^{-1}+r*i^{-1}*r^{-1}\equiv 0(mod \ p)$$

注意到：

$$
i*i^{-1}\equiv 1(mod \ p)
\\
r*r^{-1}\equiv 1(mod \ p)
$$

得到：

$$k*r^{-1}+i^{-1}\equiv 0(mod \ p)$$

化简：

$$i^{-1}\equiv -k*r^{-1}(mod \ p)$$

即：

$$i^{-1}\equiv -p/i*(p\%i)^{-1}(mod \ p)$$

为了防止负数取余，可以加上一个$p$：

$$i^{-1}\equiv (p-p/i)*(p\%i)^{-1}(mod \ p)$$

证毕。

## 关于阶乘的逆元

做卢卡斯定理的时候可能会搞出很大数字的阶乘逆元，费马小定理显得太慢，可以线性递推出所有阶乘的逆元：

```c++
fac[0]=fac[1]=1;
for(int i=2;i<=MAXN;i++)
    fac[i]=fac[i-1]*i%mod;
inv[MAXN]=qpow(fac[MAXN],mod-2);
for(int i=MAXN-1;i>=0;i--)
    inv[i]=inv[i+1]*(i+1)%mod;
```

这样考虑：

$$inv[i+1]\equiv \cfrac{1}{(i+1)!} (mod \ p)$$

那么就有：

$$inv[i+1]*(i+1)\equiv \cfrac{1}{i!}(mod \ p)=inv[i]$$

用费马小定理求出一个，其他的就可以用上式递推出来。

# 威尔逊定理

当且仅当p为素数时：

$$( p -1 )! \equiv -1 ( mod \ p )$$

素数的一个牛逼充要条件，但是不甚实用，阶乘会爆炸增长。