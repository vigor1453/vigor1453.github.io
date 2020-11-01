---
layout: post
title: "kmp和exkmp"
date: 2019-08-22
tags: ["旧","技术"]
comments: true
author: oneman233
excerpt: "必要时可以使用string的find()函数"
toc: true
---

# kmp

不妨先考虑朴素的单模式串匹配：

对于模式串：$abcab$

文本串：$abcacababcab$

此时需要寻找模式串在文本串当中的出现位置。

可以枚举文本串的每一个位置为起始位置，再向后遍历看能否完全与模式串匹配。

时间复杂度在$O(n*m)$附近。

kmp可以把时间复杂度优化到$O(n+m)$。

本质是充分利用失配位置，每一个失配位置都有一个对应的$nxt$数组，代表该位置失配之后我应该去哪里继续匹配。

但是注意的是kmp有可能被卡成$O(n*m)$，有时也许可以使用`string.find()`来获得模式串第一次出现的下标，可能还要更快。

基于上面所讲，不难发现$nxt$数组应当建立在模式串的基础上，预处理时考虑模式串第$i$位失配之后该往哪儿跳。

$nxt$数组的定义如下：

    在模式串str当中，对于每一位 str[ i ] ，它的 nxt[ i ] 应当记录的是一个位置 j ，满足以下条件：
    1. str[ i ] = str[ j ] 。
    2. j != 1 时, str[ 1 ] 到 str[ j-1 ] 与 str[ i - j + 1 ] 到 str[ i - 1 ] 完全相等。
    3. str[ j ] 是 str 的一个前缀

考察字符串$abcde$：

1. 真前缀（不包含自己的前缀）为：$a$、$ab$、$abc$、$abcd$
2. 真后缀（不包括自己的后缀）：$e$、$de$、$cde$、$bcde$

**$nxt$数组也可以理解为到$i$位置前整个模式串的真前缀和真后缀最大相同的位置。**

## 如何与文本串匹配

```c++
int j=0;
for(int i=1;i<=la;++i) {//a为文本串
    while(j&&b[j+1]!=a[i]) j=nxt[j];
    if(b[j+1]==a[i]) j++;
    if(j==lb) {
        匹配位置为i-lb+1
        j=nxt[j];
    }
}
```

## 如何建立nxt数组

实际上是模式串与自己匹配。

```c++
int j=0;
for(int i=2;i<=lb;++i){
    while(j&&b[i]!=b[j+1]) j=nxt[j];
    if(b[i]==b[j+1]) ++j;
    nxt[i]=j;
}
```

**注意如果`scanf("%s",&a+1)`，那么对应的`lena=strlen(a+1)`。**

# exkmp

扩展kmp是kmp进化版，可以获取s串与t串的所有后缀的最长公共前缀。

## 如何建立nxt数组

```c++
nxt[0]=lt;
int now=0;
while(t[now]==t[now+1]&&now+1<lt) now++;//一模一样的两行代码
nxt[1]=now;
int p=1;
for(int i=2;i<lt;++i) {
    if(i+nxt[i-p]<nxt[p]+p)
        nxt[i]=nxt[i-p];
    else {
        int now=nxt[p]+p-i;
        now=max(now,0);
        while(t[now]==t[now+i]&&now+i<lt) now++;//一模一样的两行代码
        nxt[i]=now;
        p=i;
    }
}
```

## 如何匹配

```c++
int now=0;
while(s[now]==t[now]&&now<min(lt,ls)) now++;
ex[0]=now;
int p=0;
for(int i=1;i<ls;++i) {
    if(i+nxt[i-p]<ex[p]+p)
        ex[i]=nxt[i-p];
    else {
        int now=ex[p]+p-i;
        now=max(now,0);
        while(t[now]==s[i+now]&&now<lt&&now+i<ls) now++;
        ex[i]=now;
        p=i;
    }
}
```

**注意$ex[i]$表示$s(i,len(s))$与$t$的最长公共前缀的长度。**