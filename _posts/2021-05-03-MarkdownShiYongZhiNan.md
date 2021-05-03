---
layout: post
title: "markdown使用指南"
date: 2021-05-03
tags: ["技术"]
toc: true
author: oneman233
excerpt: "优雅地写公式"
---

# 整除相关

名称|写法|效果
-|-|-
整除|\mid|$\mid$
不整除|\nmid|$\nmid$
恰整除|\parallel|$\parallel$
不恰整除|\nparallel|$\nparallel$

# 超链接

1. `shift`+`方向键`选中文字
2. 粘贴地址，自动生成超链接

# 证明

格式如下：

```
> 因为...
> 所以...
> $$
>   a=b
> $$
```

# 箭头上插入字母

写法：

```
\stackrel{R}{\longleftarrow}
```

效果：

$$
\stackrel{R}{\longleftarrow}
$$

# 花体字

写法：

```
\mathcal{A}\mathcal{B}\mathcal{C}
```

效果：

$$
\mathcal{A}\mathcal{B}\mathcal{C}
$$

# 希腊字母

# 异或和克罗内克积

写法：

```
\bigoplus \bigotimes
```

效果：

$$
\bigoplus \bigotimes
$$

# 求和和求积

写法：

```
\sum_{i=0}^n \prod_{i=0}^n
```

效果：

$$
\sum_{i=0}^n \prod_{i=0}^n
$$

# 矩阵

写法：

```
\left[
\begin{matrix}
    3 & 5 & 2 \\
    1 & 1 & 6
\end{matrix}
\right]

\left\{
\begin{matrix}
    3 & 5 & 2 \\
    1 & 1 & 6
\end{matrix}
\right\}
```

效果：

$$
\left[
\begin{matrix}
    3 & 5 & 2 \\
    1 & 1 & 6
\end{matrix}
\right]
\left\{
\begin{matrix}
    3 & 5 & 2 \\
    1 & 1 & 6
\end{matrix}
\right\}
$$

# 图片居中和图片注释

写法：

```html
<div align=center>
    <img src="图片路径"/>
    <p style="font-size:14px;color:#C0C0C0;text-decoration:underline">
        图片注释
    </p>
</div>
```