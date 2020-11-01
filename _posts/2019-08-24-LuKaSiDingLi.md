---
layout: post
title: "卢卡斯定理"
date: 2019-08-24
tags: ["旧","技术"]
comments: true
author: oneman233
excerpt: "分治组合数"
---

卢卡斯定理用于求解$c(n+m,m)\%p$，$p$为质数。

首先组合数有这样一个式子：

$$c(p,i)=p!/(i!*(p-i)!)$$

例如$c(3,2)=3!/(2!*1!)=6/2=3$。

那么：

$$c(p,i)=p/i * (p-1)!/((i-1)!*(p-i)!)$$

注意观察式子的后半段：

$$(p-1)-(i-1)=p-1-i+1=p-i$$

得到：

$$c(p,i)=p/i * c(p-1,i-1)$$

因为含有$p$作为一个因子，所以由上式可知：

$$c(p,i)\equiv p/i*c(p-1,i-1)\equiv 0(mod\  p)$$

下面会用到这一性质。

对于$c(a,b)$而言，假设：

$$a=akp^k+...+a1p+a0$$

$$b=bkp^k+...+b1p+b0$$

考虑：

$$(1+x)^p=1+c(p,1)*x+...+c(p,p-1)*x^p-1+x^p$$

由于每一个$c(p,i)$当中都含有$p$因子，得到：

$$(1+x)^p\equiv 1+x^p(mod \  p)$$

那么：

$$
(1+x)^a\equiv (1+x)^{a_0}*(1+x)^{(a_1*p)}*...*(1+x)^{(a_k*p^k)}(mod \  p)
\\
\equiv (1+x)^{a_0}*(1+x^p)^{a_1}*...*(1+x^(p^k))^{a_k}(mod \  p)
$$

得到：

$$c(a,b)=c(a_k,b_k)*...*c(a_0,b_0)(mod \  p)$$

实际上：

$$c(n,m)\%p=c(n/p,m/p)*c(n\%p,m\%p)\%p$$

前一项可以递归调用卢卡斯定理，大阶乘的逆元可以$O(n)$预处理。

模板如下：

```c++
/*
    fac[i]是预处理的阶乘数组
    我在这儿计算阶乘逆元的时候直接用的费马小定理
*/
int t,n,m,p,fac[maxn];
int c(int n,int m) {
    if(n<m) return 0;
    return (fac[n]*qpow(fac[m],p-2)*qpow(fac[n-m],p-2))%p;
}
int lucas(int n,int m) {
    if(m==0) return 1;
    return (c(n%p,m%p)%p*lucas(n/p,m/p)%p)%p;
}
```

