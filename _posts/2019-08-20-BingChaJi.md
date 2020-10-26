---
layout: post
title: "并查集"
date: 2019-08-20
tags: ["旧","技术"]
comments: true
author: oneman233
excerpt: "我的父亲的父亲是我的爷爷"
toc: true
---

# 分点

有些集合问题需要我们维护集合间的关系，如“朋友”和“敌人”两种互斥的关系，之后需要输出某些语句是否存在矛盾。

分点为此诞生，只需要三倍空间就能维护住这两种互斥关系。

例如：$A$吃$B$，$B$吃$C$，则$A$吃$C$；以及$A$和$B$是朋友两种关系。

A吃B的关系可以：

    merge(A,B+n)
    merge(A+n,B+2n)
    merge(A+2n,B)

也就是把三个集合中对应的$A$和$B$交叉合并。

朋友关系可以：

    merge(A,B)
    merge(A+n,B+n)
    merge(A+2n,B+2n)

每次检查互斥只需要判断祖先是否相同即可：

1. 如果A吃B，那么：
   * 与朋友关系互斥：`find(A)==find(B)`
   * 与B吃A关系互斥：`find(B)==find(A+n)`

2. 如果A和B是朋友，那么就与AB互相吃互斥：
   * `find(A)==find(B+n)`
   * `find(B)==find(A+n)`

# 带权

一种在并查集的节点上存储值的方法。

首先要满足以下条件：

    节点v到祖先节点的权值 = 节点v到父节点的权值 + 父节点到祖先节点的权值

在路径压缩的时候就可以更新各个节点的权值：

```c++
int _find(int x) {
    // p[x]数组存储父节点，更新后存储祖先节点
    int pr=p[x];
    int fa=_find(p[x]);
    p[x]=fa;
    val[x]+=val[pr];
}

void _merge(int x,int y,int s) {
    // 类似于从x到y添加一条权值为s的边（有向）
    if(_find(x)!=_find(y)) {
        int rx=_find(x);
        int ry=_find(y);
        val[rx]=s+val[y]-val[x];
        p[rx]=ry;
        // 不必更新x的权值，下一次find操作的时候再更新也不迟
    }
}
```

# 暴力可持久化

例如$n$个点$m$条边$k$个询问：删除$[l,r]$区间内的所有边之后联通块个数。

```c++
struct dsu{
    int p[maxn],cnt;// 父亲数组以及联通块个数
    int find(int x) {
        if(p[x]==0) return x;// 孤立的点
        p[x]=find(p[x]);
        return p[x];
    }
    void merge(x,y) {
        if(find(x)!=find(y))
            p[find(x)]=find(y),
            cnt++;
    }
}
```

对于每一条边求出它的前缀和和后缀和，分别用L和R两个并查集数组保存。

询问时，不妨令$ans = L [ l - 1 ]$。

```c++
re(i,1,n) {
    if(R[r+1].find(i)!=0)
        ans.merge(i,R[r+1].find(i))
}
```

答案为$n - ans.cnt$。

此外并查集还有一种按秩合并的优化，实际上是一种启发式合并，在dsu on tree当中有所体现。