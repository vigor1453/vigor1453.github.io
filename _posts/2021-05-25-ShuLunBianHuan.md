---
layout: post
title: "数论变换"
date: 2021-05-25
tags: ["算法"]
comments: true
toc: true
author: oneman233
excerpt: "取模的FFT"
---

# 引入

详细的fft理论可以参考[快速傅里叶变换](https://blog.mynameisdhr.com/KuaiSuFuLiYeBianHuan/)

fft可以通过把系数表达式转化成点值表达式来很好地解决多项式卷积问题，但它并不是完美的

主要缺点例如：
1. 无法取模
2. 精度受限

在fft中我们所使用的复平面上的单位根有两条重要的性质：**折半引理**和**消去引理**，这两条性质帮助我们完成了对fft的迭代计算

如果能找到另外的实数范围内的“单位根”也满足这些性质，那么是不是就可以淘汰复平面了呢？

注意：**本文介绍的数论变换基于常见模数，并且该模数必须是素数**

# 前置知识

## 模运算、整除和同余

模运算：

$$
a \ mod \ b=a-\lfloor\cfrac{a}{b}\rfloor*b
$$

整除：

$$
a|b\iff b=ka,k\in Z
$$


同余：

$$
a\equiv b(mod \ c)\iff a\%c=b\%c
$$

### 模运算的性质

$$
(a \ mod \ n)+(b \ mod \ n)=(a+b) \ mod \ n
$$

$$
(a \ mod \ n)*(b \ mod \ n)=(a*b) \ mod \ n
$$

证明如下：

设：$x=a \ mod \ n, \ y=b \ mod \ n$

根据定义：$a=k_1n+x, \ b=k_2n+y, \ k_1,k_2\in Z$

所以：$a+b=(k_1+k_2)n+(x+y),a*b=k_1k_2n^2+k_1yn+k_2xn+xy$

所以：$(a+b) \ mod \ n=x+y, \ (a*b) \ mod \ n=xy$

### 整除的性质

$$
a|ka, \ k\in Z
$$

$$
a|b\Longrightarrow \forall x\in Z, \ a|xb
$$

$$
a|b,a|c\Longrightarrow \forall x,y\in Z, \ a|(xb+yc), \ a|(xb-yc)
$$

证明使用定义即可，非常简单

### 同余的性质

$$
a\equiv b(mod \ n),b\equiv c(mod \ n)\Longrightarrow a\equiv c(mod \ n)
$$

$$
a\equiv b(mod \ n),c\equiv d(mod \ n)\Longrightarrow a+c\equiv b+d(mod \ n),a-c\equiv b-d(mod \ n),ac\equiv bd(mod \ n)
$$

证明使用下面这条定理即可：

$$
a\equiv b(mod \ n)\iff a=kn+b \ (k\in Z)
$$

### 同余的除法定理

$$
\begin{aligned}
    if \ &a,b,c,n\in Z, \ n \neq 0 \\
    and \ &gcd(a,n)=1 \\
    then \ &ab\equiv ac(mod \ n)\Longrightarrow b\equiv c(mod \ n)
\end{aligned}
$$

证明：

> $$
> \begin{aligned}
>   \because \ &gcd(a,n)=1 \\
>   \therefore \ &exist \ x,y\in Z,ax+ny=1 \\
>   \therefore \ &(b-c)(ax+ny)=b-c \\
>   &abx-acx+bny-cny=b-c \\
>   &(ab-ac)x+n(b-c)y=b-c \\
>   \because \ &ab\equiv ac(mod \ n) \\
>   \therefore \ &ab=k_1n+ac, \ k_1\in Z \\
>   \therefore \ &(ab-ac)|n \\
>   \because \ &n(b-c)y|n \\
>   \therefore \ &(ab-ac)+n(b-c)y|n \\
>   \therefore \ &b-c=k_2n, \ k_2\in Z \\
>   \therefore \ &b\equiv c(mod \ n)
> \end{aligned}
> $$

## 乘法逆元

### 定义

如果满足：

$$
a*b\equiv 1(mod \ c)
$$

称：$b$是$a$在模$c$意义下的乘法逆元，记为：$b=a^{-1}(mod \ c)$

### 模意义下的除法

$$
\cfrac{a}{b}\equiv a*b^{-1}(mod \ p)
$$

### 求解方法

#### 扩展欧几里得算法

扩展欧几里得算法可以用于求解形如$ax\equiv b(mod \ c)$的线性同余方程

不难看出逆元的定义就是一个特殊的线性同余方程，完全套用扩展欧几里得算法即可

#### 递推关系

参考[线性递推](https://blog.mynameisdhr.com/NiYuanEXGCDYiJiWeiErXunDingLiDengDeng/)和[求任意n个数的逆元](https://oi-wiki.org/math/inverse/)

#### 阶乘的逆元

参考[阶乘逆元](https://blog.mynameisdhr.com/NiYuanEXGCDYiJiWeiErXunDingLiDengDeng/)

## 费马小定理

如果$p$是一个素数，并且$gcd(a,p)=1$，则：

$$
a^{p=1}\equiv 1(mod \ p)
$$

证明如下：

设有一整数集合$S={1,2,...,p-1}$，函数$\Psi(x)=ax(mod \ p), \ x\in S$
显然有：$\Psi(x)<p$

首先证明：**$\forall x, \ \Psi(x)\in S$**

假设：$\Psi(x)=0$，即：$ax\equiv 0(mod \ p)$

因为：$gcd(a,p)=1$,根据除法定理得到：$x\equiv 0(mod \ p)$

这与$x\in S$相矛盾

第二步证明：$\forall x,y\in S,x\neq y\Longrightarrow \Psi(x)\neq \Psi(y)$

假设：$exist \ x\neq y,\Psi(x)=\Psi(y)$，即：$ax\equiv ay(mod \ p)$

因为：$gcd(a,p)=1$,根据除法定理得到：$x\equiv y(mod \ p)$

即：$x-y=kp,\ k\in Z$

又因为：$x,y\in \{1,2,...,p-1\}$，所以：$k=0$

即：$x=y$，这与假设矛盾

通过以上两步证明可知：把$\forall x\in \{1,2,...,p-1\}$代入$\Psi(x)$得到的输出其实就是集合$\{1,2,...,p-1\}$

那么两个集合中的元素之积也应该相等，即：

$$
\begin{aligned}
  \prod_{i=1}^{p-1}i=&\prod_{i=1}^{p-1}\Psi(i) = (a*1) \ mod \ p*...*(a*(p-1)) \ mod \ p
\end{aligned}
$$

根据模运算的乘法性质可得：

$$
\prod_{i=1}^{p-1}i\equiv a^{p-1}*(\prod_{i=1}^{p-1}i)(mod \ p)
$$

因为：$p$是一个素数，显然有：$gcd(p,j)=1, \ j\in \{1,...,p-1\}$

反复使用除法定理可得：

$$
a^{p-1}\equiv 1(mod \ p)
$$

实际上费马小定理是**欧拉定理**的一个特例，它们的证明方法也类似

### 与逆元的关系

对于：整数$a$和素数$p$，如果：$gcd(a,p)=1$

根据费马小定理：$a*a^{p-2}\equiv 1(mod \ p)$

那么：$a^{p-2}$是$a$在模$p$意义下的乘法逆元

## 原根

对于素数$p$，它的原根$a$满足以下两个重要条件：

1. $a^1 \ mod \ p\neq a^2 \ mod \ p\neq...\neq a^{p-1} \ mod \ p$
2. $\forall i\in\{1,2,...,p-1\}, \ a^i \ mod \ p\in \{1,2,...,p-1\}$

本原根就是：**最小的原根**

注意，实际上非素数也有自己的原根

# 与fft的等价性证明

注意：**以下所有运算过程都是在模p意义下完成的**

我们取单位根为：

$$
w_n^k=g^{\cfrac{k(p-1)}{n}}
$$

其中：$g$是$p$的原根

## 基本性质

$$
w_n^n=g^{\cfrac{n(p-1)}{n}}=g^{p-1}\equiv 1(mod \ p)
$$

证明使用费马小定理即可

## 折半引理

$$
w_{2n}^{2k}=g^{\cfrac{2k(p-1)}{2n}}=g^{\cfrac{k(p-1)}{n}}=w_n^k
$$

## 消去引理

$$
\begin{aligned}
    w_{n}^{k+n/2}&=g^{\cfrac{(k+n/2)(p-1)}{n}} \\
    &=g^{\cfrac{k(p-1)}{n}}*g^{\cfrac{(n/2)(p-1)}{n}} \\
    &=w_n^k*g^{\cfrac{p-1}{2}} \\
    \because \ &(g^{\cfrac{p-1}{2}})^2=g^{p-1}\equiv 1(mod \ p) \\
    and \ &g^{\cfrac{p-1}{2}}\neq g^{p-1} \\
    \therefore \ &g^{\cfrac{p-1}{2}}\equiv -1(mod \ p) \\
    \therefore \ &w_{n}^{k+n/2}\equiv -w_n^k(mod \ p)
\end{aligned}
$$

上面的证明过程中有两点注意：
1. 当：$p\neq 2$时，显然：$\cfrac{p-1}{2}\in Z$，利用原根的性质可知：$g^{\cfrac{p-1}{2}}\neq g^{p-1}$
2. 实际上求模意义下的开根是一个经典问题：**二次剩余**，它可以用[Cipolla 算法](https://oi-wiki.org/math/quad-residue/)求解

## ntt的逆过程

计算方法和fft几乎完全一致，**通过代入$w_n^{-k}$再做一次ntt**，最后得到结论：

$$
c_k=a_k*n(mod \ p)
$$

区别在于：**$w_n^{-k}$是$w_n^k$的乘法逆元**

并且要注意：**计算除以$n$时应当乘以$n$的乘法逆元**

# c语言实现

[测试题在这里](https://www.luogu.com.cn/problem/P3803)

注意：**本地运行第八组数据$1.8s$结束，但是在线评测就会TLE**，我也搞不懂是什么意思

```c
#include <stdio.h>
#define MAXN 5000005
typedef long long ll;

ll qpow(ll a,ll b,ll mod) {
	ll ret=1;
	while(b) {
		if(b&1) ret=ret*a%mod;
		a=a*a%mod;
		b>>=1;
	}
	return ret;
}

ll MOD=998244353ll,g=3ll,ginv=332748118ll;
#define swap(x,y) x^=y,y^=x,x^=y;

int ord[MAXN];
void ntt(ll a[],int n,int flg) {
	int i=0,j=0;
	for(;i<n;++i) {
		ord[i]=j;
		int mask=n>>1;
		for(;j&mask;mask>>=1) j&=~mask;j|=mask;
		if(i<ord[i]) swap(a[i],a[ord[i]]);
	}
	int mid=1;
	for(;mid<n;mid<<=1) {
		ll W=qpow((~flg)?g:ginv,(MOD-1)/(mid<<1),MOD);
		for(i=0;i<n;i+=mid<<1) {
			ll w=1,x,y;
			for(j=i;j<i+mid;++j,w=w*W%MOD)
				x=a[j],y=w*a[j+mid]%MOD,
				a[j]=(x+y)%MOD,
				a[j+mid]=(x-y+MOD)%MOD;
		}
	}
	if(!(~flg)) {
		ll inv=qpow(n,MOD-2,MOD);
		for(i=0;i<n;++i) a[i]=a[i]*inv%MOD;
	}
}

int n,m,lmt=1;
ll a[MAXN],b[MAXN];

int main() {
	scanf("%d%d",&n,&m);
	int i=0;
	for(;i<=n;++i) scanf("%lld",a+i);
	for(i=0;i<=m;++i) scanf("%lld",b+i);
	while(lmt<=n+m) lmt*=2;
	ntt(a,lmt,1);
	ntt(b,lmt,1);
	for(i=0;i<lmt;++i) a[i]=a[i]*b[i]%MOD;
	ntt(a,lmt,-1);
	for(i=0;i<=n+m;++i) printf("%lld ",a[i]);
	return 0;
}
```

一些注意：
1. swap函数写成了**异或**的形式，方便调用
2. 计算$w_n^{-k}$时，可以**提前计算好$g^{-1} \ mod \ p$**
3. **原根为$3$的素数**有：$469762049$、$998244353$、$1004535809$

# 参考

1. [比FFT还容易明白的NTT（快速数论变换）](https://blog.csdn.net/enjoy_pascal/article/details/81771910)
2. [快速数论变换（NTT）](https://blog.csdn.net/zz_1215/article/details/40430041)
3. [乘法逆元](https://oi-wiki.org/math/inverse/)
4. [逆元、exgcd以及威尔逊定理等等](https://blog.mynameisdhr.com/NiYuanEXGCDYiJiWeiErXunDingLiDengDeng/)