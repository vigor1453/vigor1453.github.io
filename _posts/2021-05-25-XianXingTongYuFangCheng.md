---
layout: post
title: "线性同余方程"
date: 2021-05-25
tags: ["算法"]
comments: true
toc: true
author: oneman233
excerpt: "特别的方程"
---

# 定义

形如$ax\equiv c \ (mod\ b)$的方程被称为**线性同余方程**

# 前置知识

## 辗转相除法

为了求解线性同余方程，先要介绍欧几里得求解最大公约数的算法，又称为辗转相除法：

$$
gcd(a,b)=gcd(b,a \ mod \ b) \ (a \ > \ b)
$$

它的简要证明如下：

> 假设：$a=bk+c,k\in Z$
> 那么显然有：$c=a \ mod \ b=a-bk$
> 令：$d|a,d|b$
> 观察等式：$\frac{c}{d}=\frac{a}{d}-\frac{b}{d}k$
> 因为：$\frac{a}{d},\frac{b}{d},k\in Z$
> 所以：$\frac{c}{d}\in Z$
> 即：$d|c$

上面的证明说明了：**凡是$a$、$b$的公约数，同时也是$b$、$a \ mod \ b$的公约数**

同理可证：**凡是$b$、$a \ mod \ b$的公约数，同时也是$a$、$b$的公约数**

既然它们的公约数都相同，那么最大公约数也相同，证毕

由于**规定$gcd(a,0)=a$**，所以辗转相除法可以递归进行，当发现$a \ mod \ b=0$时就退出即可

## 最大公约数与质因数分解

最大公约数也可以从质因数分解的角度来考虑：

$$
\begin{aligned}
    & a=\prod_{i=0}^np_i^{a_i}, \ b=\prod_{i=0}^mp_i^{b_i} \\
    & \forall i, \ p_i \ \ is \ \ prime \\
    & gcd(a,b)=\prod_{i=0}^{max(n,m)}p_i^{min(a_i,b_i)}
\end{aligned}
$$

证明如下：

> 令：$d|a$
> 那么对于d的质因数分解形式：
> $$
>   \prod_{i=0}^Op_i^{d_i}
> $$
> 显然有：$d_i<=a_i$
> 同理有：$d_i<=b_i$
> 即：$d_i<=min(a_i,b_i)$
> 假设：$d=gcd(a,b)$，则：$d$必须尽量大
> 所以：$d_i=min(a_i,b_i)$

此外最大公约数还有一个性质：

$$
gcd(ac,bc)=c*gcd(a,b) \ (c>1)
$$

利用最大公约数的质因数分解形式即可完成这个性质的证明：

> 考虑$a$、$b$、$c$的质因数分解形式：
> $$
>   a=\prod_{i=0}^np_i^{a_i}, \ b=\prod_{i=0}^mp_i^{b_i}, \ c=\prod_{i=0}^Op_i^{c_i}
> $$
> 那么有：
> $$
> \begin{aligned}
>   gcd(ac,bc)= & \ \prod_{i=0}^{max(n,m,O)}p_i^{min(a_i+c_i,b_i+c_i)} \\
>             = & \ \prod_{i=0}^{max(n,m,O)}p_i^{min(a_i,b_i)+c_i} \\
>             = & \ \prod_{i=0}^{max(n,m,O)}p_i^{min(a_i,b_i)}*p_i^{c_i} \\
>             = & \ c*\prod_{i=0}^{max(n,m,O)}p_i^{min(a_i,b_i)} \\
>             = & \ c*gcd(a,b) \\
> \end{aligned}  
> $$

用上面这个性质可以获得下面这个推论：

$$
\begin{aligned}
    & d=gcd(a,b), \ a=k_1d, \ b=k_2d \\
    & gcd(k_1,k_2)=1
\end{aligned}
$$

## 裴蜀定理

裴蜀定理的内容是：

$$
\begin{aligned}
    &\forall a,b\in Z, \ gcd(a,b)=d \\
    &\exist x,y\in Z,ax+by=c \Longleftrightarrow d|c
\end{aligned}
$$

证明如下：

> 因为：$d=gcd(a,b)$
> 所以：$d|a,d|b$
> 所以：$\forall x,y\in Z,d|ax+by$
> 假设：$s$是$ax+by$的最小正值，令：$q=\lfloor\frac{a}{s}\rfloor$
> 则：$r=a\%s=a-q(ax+by)=a(1-qx)+b(-qy)$
> 说明：$r$也满足$ax+by$的形式
> 由于：$r\in[0,s)$，并且：$s$是$ax+by$的最小正值
> 所以：$r=0$
> 所以：$s|a$，同理：$s|b$
> 而由于：$d=gcd(a,b)$，所以：$d\geq s$
> 又因为：$d|ax+by$，即：$d|s$
> 由于：$s>0$，所以：$d\leq s$
> 由此得出结论：$d=s$
> 既然$d$是$ax+by$的最小正值，而又显然有：$d|ax+by$，所以：**当$d\nmid c$时，方程无解**

裴蜀定理还有一个重要的推论：

$$
\begin{aligned}
    &\forall a,b\in Z, (a=0\&\&b=0)=false \\
    &\exist \ x,y\in Z,ax+by=gcd(a,b)
\end{aligned}
$$

证明如下：

1. 如果$a=0$或者$b=0$：
   
> 此时原方程转化为$ax=a$或者$by=b$，定理显然成立
2. 否则：

> 由于$gcd(a,b)=gcd(a,-b)$，不妨假设：$a\geq b,a>0,b>0$
> 令：$d=gcd(a,b)$
> 对于$ax+by=d$，两边同时除以$d$可得：$a_1x+b_1y=1$
> 由gcd性质的推论，此时有：$gcd(a_1,b_1)=1$，下面试图证明这个式子
> 把辗转相除法的过程展开：
> $$
> \begin{aligned}
>   &a_1=q_1b_1+r_1 \ (r_1\in[0,b_1)) \\
>   &b_1=q_2r_1+r_2 \ (r_2\in[0,r_1]) \\
>   &... \\
>   &r_{n-3}=q_{n-1}r_{n-2}+r_{n-1} \\
>   &r_{n-2}=q_nr_{n-1}+r_n \\
>   &r_{n-1}=q_{n+1}r_n+0 \\
> \end{aligned}
> $$
>
> 即：
> $$gcd(a_1,b_1)=gcd(b_1,r_1)=...=gcd(r_{n-1},r_n)=1$$
> 利用辗转相除法的性质：
> $$
> \begin{aligned}
>   & gcd(r_{n-1},r_n)=1 \\
>   & gcd(q_{n+1}r_n,r_n)=1 \\
>   & r_n*gcd(q_{n+1},1)=1 \\
>   & r_n=1
>\end{aligned}
> $$
> 得到了$r_n=1$之后，我们考虑把这个结果回代，消去$r_n$：
> $$
>   r_{n-2}=q_nr_{n-1}+1
> $$
> 也即：
> $$
>   1=r_{n-2}-q_nr_{n-1}
> $$
> 继续回代，消去$r_{n-1}$：
> $$
>   1=r_{n-2}-q_n(r_{n-3}-q_{n-1}r_{n-2})=(1+q_nq_{n-1})r_{n-2}-q_nr_{n-3}
> $$
> 重复上述过程，逐步消去$r_{n-2}...r_1$，最终可以获得形如：$1=a_1x+b_1y$的等式
> 这说明：**方程$1=a_1x+b_1y$有解**，也就证明了：$gcd(a_1,b_1)=1$

## 扩展欧几里得

实际上裴蜀定理的证明过程就是扩展欧几里得算法的思路，考虑以下两个等式：

$$
\begin{aligned}
    & ax+by=gcd(a,b) \\
    & bx_1+(a\%b)y_1=gcd(b,a\%b)=gcd(a,b)
\end{aligned}
$$

则有：

$$
\begin{aligned}
    ax+by&=bx_1+(a\%b)y_1 \\
    &=bx_1+(a-kb)y_1 \\
    &=bx_1+ay_1-kby_1 \\
    &=ay_1+b(x_1-ky_1) \\
    &(k=\lfloor\frac{a}{b}\rfloor)
\end{aligned}
$$

这样可以考虑在函数向下递归之后回溯时更新$x$和$y$，用来求解一组满足方程：$ax+by=gcd(a,b)$的解

c实现代码如下：

```c
/*
    返回值是最大公约数
*/
ll exgcd(ll a,ll b,ll *x,ll *y) {
	if(!b) {
		*x=1ll;
		*y=0ll;
		return a;
	}
	ll ret=exgcd(b,a%b,x,y);
	ll tmp=*x-a/b*(*y);
	*x=*y;
	*y=tmp;
	return ret;
}
```

这里注意一点，**没有必要考虑$a$和$b$的大小关系**

假设$a<b$，在第一次扩展欧几里得中，函数会如下运行：

$$
gcd(a,b)=gcd(b,a\%b)=gcd(b,a) \ (a<b)
$$

函数就这样自动完成了$a$和$b$的交换

# 解法

## 定理一

方程$ax+by=c$与方程$ax\equiv c(mod \ b)$等价，有整数解的充要条件是$gcd(a,b)|c$

方程的等价性显然，有整数解的充要条件参考裴蜀定理

## 定理二

若$x_0,y_0$是方程：$ax+by=c$的一组特解，那么：**方程的通解可以表示为$x_0+bt,y_0-at \ (t\in Z)$**

为了满足$x_0+bt,y_0-at\in Z$，显然：**$t$的最小值为：$\cfrac{1}{gcd(a,b)}$**

这也就意味着：**$x$的最小正整数解为$x_0+\cfrac{b}{gcd(a,b)}$**

注意一点：**特解$x$是正整数时就不需要加上$\cfrac{b}{gcd(a,b)}$了**

完整c代码如下：

```c
#include <stdio.h>
typedef long long ll;

ll exgcd(ll a,ll b,ll *x,ll *y) {
	if(!b) {
		*x=1ll;
		*y=0ll;
		return a;
	}
	ll ret=exgcd(b,a%b,x,y);
	ll tmp=*x-a/b*(*y);
	*x=*y;
	*y=tmp;
	return ret;
}

int lieq(ll a,ll b,ll c,ll *x,ll *y) {
	ll d=exgcd(a,b,x,y);
	if(c%d) return 0;
	(*x)*=c/d;
	(*y)*=c/d;
	if(*x<=0) (*x)+=b/d;
	return 1;
}

ll a,b;

int main() {
	scanf("%lld%lld",&a,&b);
	ll x=0,y=0;
	lieq(a,b,1,&x,&y);
	printf("%lld",x);
	return 0;
}

```

测试题目：[#2605. 「NOIP2012」同余方程](https://loj.ac/problem/2605#submit_code)

# 参考

1. [最大公约数](https://oi-wiki.org/math/gcd/)
2. [GCD from Prime Decomposition](https://proofwiki.org/wiki/GCD_from_Prime_Decomposition)
3. [裴蜀定理](https://oi-wiki.org/math/bezouts/)
4. [线性同余方程](https://oi-wiki.org/math/linear-equation/)