---
layout: post
title: "单调队列"
date: 2019-08-18
tags: ["旧","算法"]
comments: true
author: oneman233
excerpt: "总是单调递增或递减"
---

需要维护以下两种信息：

1. 原数组某些值和这些值对应的下标
2. 保证这些值按照递增或递减有序排列

可以使用单调队列。

```c++
/*
    head tail两个指针分别表示队列的头尾位置
    p数组储存原数组的下标
    q数组储存原序列中的值
*/
int head=1,tail=0;//初始为空
for(int i=1;i<=n;++i){
    while(head<=tail&&q[tail]<=a[i])//保证递增
        tail--;//去尾
    q[++tail]=a[i];
    p[tail]=i;
    while(p[head]<=i-k)
        head++;//去头
}
```

**此时队列中的最大值为$q[head]$，整个队列最大的容量为$k$。**