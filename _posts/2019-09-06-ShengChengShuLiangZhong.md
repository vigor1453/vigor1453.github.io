---
layout: post
title: "生成树两种"
date: 2019-09-06
tags: ["旧","算法"]
comments: true
author: oneman233
excerpt: "图变树"
toc: true
---

生成树：

    从一张包含n个节点的图中选出n-1条边，使得全部点可以被这n-1条边联通且不包含环
    也即这n-1条边构成一棵树
    这棵树称为原图的生成树

最小生成树就是所有生成树中边权总和最小的那一个。

求解最小生成树主要有两个算法，prim和kruskal，前者适用于稠密图，后者适用于稀疏图。

# prim

大概步骤如下：

1. 任选一个起点开始算法
2. 不断加入与当前生成树相邻且权值最小的边
3. 直到所有点都在树中，停止算法

使用两个辅助数组：$dis[maxn]$和$fa[maxn]$

第一个数组保存某个顶点距离生成树的最小权值，第二个数组保存某个顶点在生成树当中的父节点。

```c++
int prim(int s)
{
	int sum=0;
	int cnt=0;
	for(int i=1;i<=n;++i)
		dis[i]=mp[s][i];
	cnt++;
	while(1)
	{
		int mn=inf;
		int now=-1;
		for(int i=1;i<=n;++i)
		{
			if(dis[i]!=0&&dis[i]<mn)
			{
				mn=dis[i];
				now=i;
			}
		}
		if(now==-1) break;
		sum+=dis[now];
		dis[now]=0;
		cnt++;
		for(int i=1;i<=n;++i)
		{
			if(dis[i]!=0&&mp[now][i]<dis[i])
				dis[i]=mp[now][i];
		}
	}
	if(cnt<n) return -1;
	else return sum;
}
```

# kruskal

把边按照权值排序，由小到大，每次贪心地取出边权最小的边。

用并查集维护一下点的联通性，如果当前边两个节点不在同一个集合则合并他们并且累加对答案的贡献。

```c++
int kk()
{
	for(int i=1;i<=n;++i)
		f[i]=i;
	sort(o+1,o+1+m,cmp);
	int sum=0;
	for(int i=1;i<=m;++i)
	{
		if(_find(o[i].x)!=_find(o[i].y))
		{
			sum+=o[i].z;
			_merge(o[i].x,o[i].y);
		}
	}
	int tmp=_find(1);
	for(int i=2;i<=n;++i)
		if(_find(i)!=tmp)
			return -1;
	return sum;
}
```

# 一个trick

在点权与边权同时存在时，可以考虑建立一个$0$节点。

把$0$节点到其他所有节点连一条权值为点权的边，这样再跑最小生成树就可以消除点权的影响。