---
layout: post
title: "数论分块和取余的本质"
date: 2019-09-16
tags: ["旧","算法"]
comments: true
author: oneman233
excerpt: "把数字切细碎点"
---

当有一个$k=19$时，看看下面这两个序列：

$$
\begin{aligned}
    i: & \{1 \ \ \  2 \ 3 \ 4 \ 5 \ 6 \ 7 \ 8 \ 9 \ 10 \ 11 \ 12 \ 13 \ 14 \ 15 \ 16 \ 17 \ 18 \ 19\} \\
    \lfloor k/i \rfloor: & \{19 \ 9 \ 6 \ 4 \ 3 \ 3 \ 2 \ 2 \ 2 \ 1 \ \ \ 1 \ \ \ 1 \ \ \ 1 \ \ \ 1 \ \ \ 1 \ \ \ 1 \ \ \ 1 \ \ \ 1 \ \ \ 1 \ \ \}
\end{aligned}
$$

可以发现实际上$\lfloor k/i \rfloor$满足某种“块状”的性质，即**某些连续部分的答案是相同的**。

这就是数论分块，结论如下：

>   对任意类似的区间，假设其左边界为$L$，右边界为$R$
>   那么$R=\lfloor {( k/\lfloor k/L \rfloor )} \rfloor$
>   每次只需要令$L=R+1$即可移动到下一块

代码如下：

```c++
/*
    L，R分别代表当前左右区间
    n是枚举的范围
    k是当前的被除数
    并且当前块内所有值都为t
*/
int L=1,R=0,ans=0;
    do{
//    	cout<<L<<' '<<R<<endl;
		int t=k/L;
		if(t!=0) R=min(k/t,n);
		else R=n;
		ans+=((R-L+1)*(R+L)/2)*t;//计算答案
		L=R+1;
	}while(L<=n);
```

在某些题目中数论分块可以把时间压缩到$O(\sqrt{n})$。

另外还有一个结论：

>   $a\%b=a-b* \lfloor a/b \rfloor$

数论分块和这个结论结合之后就可以解决：[[CQOI2007]余数求和](https://www.luogu.org/problem/P2261)。

另外再贴三个公式，往往在数论分块中求出对答案的贡献：

$$\sum_{i=1}^{n} i =(1+n)*n/2$$

$$\sum_{i=1}^{n} i^2 =(2n+1)*(n+1)*n/6$$

$$\sum_{i=1}^{n} i^3 ={(\sum_{i=1}^{n} i)}^2$$

它们结合之后就可以解决：[P2424 约数和](https://www.luogu.com.cn/problem/P2424)。