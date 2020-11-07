---
layout: post
title: "字符串哈希"
date: 2019-08-18
tags: ["算法"]
comments: true
author: oneman233
excerpt: "把文字变成数字"
toc: true
---

# 预定义

定义关于字符x的函数：

$$idx(x)=x-'a'+1$$

也可以直接使用x的ASCII码值。

# 哈希方法

## 自然溢出

利用unsigned long long溢出后会回到0的性质建立哈希:
$$hash[i]=hash[i-1]*p+idx(s[i])(p是选取的素数)$$


## 单哈希

$$hash[i]=(hash[i-1]*p+idx(s[i]))(p和mod是选取的素数，p<=mod)%mod$$

## 双哈希

$$h1[i]=(h1[i-1]*p+idx(s[i]))%mod1$$
$$h2[i]=(h2[i-1]*p+idx(s[i]))%mod2$$

哈希值是：$(h1,h2)$

# 子串哈希值

对于子串$(l,r)$，其哈希值为：

$$(hash[r]-hash[l-1]*p^{(r-l+1)}+MOD) \% {MOD}$$

注意减法那儿有负数取余的风险。

# 其他tips

1. 要想严格$O(1)$获取子串哈希值可以预处理一下$p$的次方。
2. $p$选择在$49157$是比较优秀的，碰撞率低于$0.01\%$。