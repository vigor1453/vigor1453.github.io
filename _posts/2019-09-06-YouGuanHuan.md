---
layout: post
title: "有关环"
date: 2019-09-06
tags: ["旧","技术"]
comments: true
author: oneman233
excerpt: "南辕北辙"
toc: true
---

# 最小环

首先说这样一个问题：

    给定带边权无向图，求出该图中“最小环”。
    其中，最小环要求至少由三个点构成，并且环上各边的边权之和最小。

~~提一句找负环用spfa。~~

基本方法有两种，floyd和并查集：

## floyd

首先考虑朴素的floyd：

```c++
for(int k=1;k<=n;++k)
    for(int i=1;i<=n;++i)
        for(int j=1;j<=n;++j)
            d[i][j]=min(d[i][j],d[i][k]+d[k][j]);
```

枚举中点和两端点，更新最短距离。

注意**枚举顺序很重要**。

如果把$k$放在最内层，可能出现更新$d[i][j]$的时候$d[i][k]$还未更新的情况，此时每次更新只能更新相邻的一个位置。

现在考虑怎么找环：

假设有三个点按照从小到大的顺序排列好，并且它们互不相等：

$$i\rightarrow j\rightarrow k$$

我们希望检查这三个点是否构成环，条件如下：

    如果d[i][j]+e[j][k]+e[k][i]不为inf，则这三点成环。
    （e为原始边权，d为最短路）

这意味着我不关心$i\rightarrow j$的最短路形态如何，我只关心在$j\rightarrow k$和$k\rightarrow i$这相邻的两步当中有没有构成环的可能性。

初始把**d和e数组都设置为inf**再读入数据，过程中只更新d数组：

```c++
void floyd() {
    for(int k=1;k<=n;++k) {
        int mn=inf;
        for(int i=1;i<k;++i)
            for(int j=i+1;j<k;++j)
                mn=min(mn,d[i][j]+e[j][k]+e[k][i]);
        if(mn!=inf)
            // 有一个最小环长度为mn
        for(int i=1;i<=n;++i)
            for(int j=1;j<=n;++j)
                d[i][j]=min(d[i][j],d[i][k]+d[k][j]);
    }
}
```

上面的找环用于无向图，利用了无向图的对称性质，保证$i$、$j$、$k$三个点能够检查所有可能的环。

这种方法也可以拓广到有向图的情况，两个步骤：

1. 把$d$数组初始为$inf$，读入边权，跑一遍$floyd$
2. 遍历$d[i][i]$，如果$d[i][i]$不为$inf$，那么更新最小环的答案

**注意这种求解最小环的方法不能控制环上的点数至少为3。**

## 并查集

三个步骤：

1. 用一个$d$数组保存当前节点到根节点的距离
2. 如果新加的边的端点不连通，则连接他们并且令$d[x]=d[y]+1$
3. 如果新加的边联通，更新最小环答案为$d[x]+d[y]+1$

类似于带权并查集的写法：

```c++
int _find(int x) {
    if(x!=p[x]) {
        int tmp=p[x];
        p[x]=_find(p[x]);
        d[x]+=d[tmp];
        /*
            相当于原来的d只存储了x到tmp的距离
            现在要更新到根的距离的话必须这样写
        */
 }
 return p[x];
}
void _merge(int x,int y) {
    int rx=_find(x);
    int ry=_find(y);
    if(rx!=ry){ p[rx]=ry; d[x]=d[y]+1; }
    else ans=min(ans,d[x]+d[y]+1);
}
```

**一定要在main函数当中把p数组初始化为p[i]=i。**

# 无向图找环

## dfs

```c++
bool dfs(int x,int f) {
    for(int i=0;i<g[x].size();++i) {
        int v=g[x][i];
        if(v==f) continue;
        if(vis[v]==0) {
            vis[v]=1;
            if(dfs(v,u)) return 1;
        }
        else return 1;
    }
return 0;
}
```

考虑到可能未联通的情况，应当对每个未访问的节点$i$都进行一次`dfs(i,-1)`。

本质上是对每个节点判断一下有没有回头的反向边。

## 并查集

与找最小环类似，但是不需要记录权值，把相连的两个节点合并，一旦查到有相同祖先的节点之间有边就说明有环。

# 有向图找环

## dfs

```c++
void dfs(int x,int f) {
    if(v[x]==2) // f到x是环的最后一条边
    v[x]=2;
    for(int i=0;i<g[x].size();++i) {
        int y=g[x][i];
        if(v[y]==0)
            dfs(y,x);
        else if(v[y]==2) // x到y是环的最后一条边
    }
    v[x]=1;
}
```

老样子，为了搞定不连通的情况，需要对每个$v[i]=0$执行`dfs(i,-1)`。

## 拓扑排序

三个步骤：

1. 从图中选择一个入度为$0$的点$x$，给他打上标记$v[x]=1$
2. 删除该点的所有出边
3. 如果出边对应的终点已经被删除过，那么当前出边是环的最后一条边

照着拓扑排序的板子打，在板子上加一个出边终点是否访问过的判断就行。

# 奇环

**看到奇环就想到二分图染色**，2019ICPC上海的签到。