---
layout: post
title: "矩阵的幂"
date: 2019-09-10
tags: ["旧","算法"]
comments: true
author: oneman233
excerpt: "上帝视角"
toc: true
---

矩阵有很多神秘的用处。

对于两个n阶矩阵a，b，它们相乘的算法如下：

```c++
for(int k=1;k<=n;++k)
    for(int i=1;i<=n;++i)
        for(int j=1;j<=n;++j)
            c[i][j]+=a[i][k]*b[k][j];
```

两个说明：

1. 矩阵乘法是前一个矩阵的每一行的元素与后一个矩阵的每一列的元素依次相乘
2. 单位矩阵乘以任何矩阵A都等于A

# 性质一

对一张边权全为$1$的图建立一个邻接矩阵：

$$
\left\{
\begin{matrix}
    0 & 1 & 1 \\
    1 & 0 & 0 \\
    1 & 0 & 0
\end{matrix}
\right\}
$$

**如果$i$、$j$间有$k$条边标记为$k$，否则标记为$0$**。

以上邻接矩阵代表原图中只有$1\rightarrow2$、$1\rightarrow3$、$2\rightarrow1$和$3\rightarrow1$四条边。

那么有这样一个结论：

**邻接矩阵的$n$次方代表了用$n$步从$i$走到$j$有多少种不同的方法（允许重边）**。

以上邻接矩阵的平方为：

$$
\left\{
\begin{matrix}
    2 & 0 & 0 \\
    0 & 1 & 1 \\
    0 & 1 & 1
\end{matrix}
\right\}
$$

不难发现从$1$到$1$走两步共有两种走法：$1\rightarrow2\rightarrow1$和$1\rightarrow3\rightarrow1$。

又例如邻接矩阵：

$$
\left\{
\begin{matrix}
    0 & 1 & 1 \\
    0 & 0 & 1 \\
    0 & 0 & 0
\end{matrix}
\right\}
$$

表示原图中只有$1\rightarrow2$、$1\rightarrow3$和$2\rightarrow3$三条边。

它的平方为：

$$
\left\{
\begin{matrix}
    0 & 0 & 1 \\
    0 & 0 & 0 \\
    0 & 0 & 0
\end{matrix}
\right\}
$$

这代表着从$1$到$3$走两步只有一种走法：$1\rightarrow2\rightarrow3$。

# 性质二

矩阵快速幂可以用来加速递推式。

例如经典的斐波那契数列递推公式：

$$
f_n=f_{n-1}+f_{n-2},f_0=0,f_1=1
$$

实际上手推一下可以发现下面这个式子：

$$
\left\{
\begin{matrix}
    f_n & f_{n-1}
\end{matrix}
\right\}={
    \left\{
    \begin{matrix}
        f_{n-1} & f_{n-2}
    \end{matrix}
    \right\}*{
        \left\{
        \begin{matrix}
            1 & 1 \\
            1 & 0
        \end{matrix}
        \right\}
    }
}
$$

进一步推广：

$$
\left\{
\begin{matrix}
    f_n & f_{n-1}
\end{matrix}
\right\}={
    \left\{
    \begin{matrix}
        f_1 & f_0
    \end{matrix}
    \right\}*{
        \left\{
        \begin{matrix}
            1 & 1 \\
            1 & 0
        \end{matrix}
        \right\}
    }^{n-2}
}
$$

这个结论也可以应用到其他的多项递推式上，之后就可以利用快速幂加速计算了。

快速幂参见：[快速幂](https://blog.mynameisdhr.com/KuaiSuMi/)。

# 性质三

对于一些一个序列上的操作可以用矩阵维护住并且加速。

例如对于以下序列：

$$
A=
\left\{
\begin{matrix}
    a_1 & a_2 & a_3
\end{matrix}
\right\}
$$

给$A$右乘一个矩阵：

$$
\begin{aligned}
    & A*\left\{
    \begin{matrix}
        1 & 0 & 0 \\
        0 & 0 & 1 \\
        0 & 1 & 0
    \end{matrix}
    \right\} \\
    & = \left\{
    \begin{matrix}
        a_1 & a_2 & a_3
    \end{matrix}
    \right\}*\left\{
    \begin{matrix}
        1 & 0 & 0 \\
        0 & 0 & 1 \\
        0 & 1 & 0
    \end{matrix}
    \right\} \\
    & = \left\{
    \begin{matrix}
        a_1 & a_3 & a_2
    \end{matrix}
    \right\}
\end{aligned}
$$

不难发现右乘这个矩阵实际上把$A$的**第二位和第三位交换了位置**。

又例如对于一个序列：

$$
A'=
\left\{
\begin{matrix}
    a_1 & a_2 & 1
\end{matrix}
\right\}
$$

给$A'$右乘一个矩阵：

$$
\begin{aligned}
    & A'*\left\{
    \begin{matrix}
        1 & 0 & 0 \\
        0 & 1 & 0 \\
        0 & 1 & 1
    \end{matrix}
    \right\} \\
    & = \left\{
    \begin{matrix}
        a_1 & a_2 & 1
    \end{matrix}
    \right\}*\left\{
    \begin{matrix}
        1 & 0 & 0 \\
        0 & 0 & 1 \\
        0 & 1 & 0
    \end{matrix}
    \right\} \\
    & = \left\{
    \begin{matrix}
        a_1 & a_2+1 & 1
    \end{matrix}
    \right\}
\end{aligned}
$$

不难发现右乘这个矩阵实际上把$A'$的**第二位增加了$1$**。