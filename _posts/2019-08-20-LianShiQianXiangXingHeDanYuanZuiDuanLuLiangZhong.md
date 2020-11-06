---
layout: post
title: "链式前向星和单源最短路两种"
date: 2019-08-20
tags: ["旧","算法"]
comments: true
author: oneman233
excerpt: "如何记下地图并且以最快速度回家"
toc: true
---

链式前向星是一种对边进行离散化存储的方案，基本方法有vector和数组两种。

需要注意的是，边数极大时数组的访问数组甚至可能劣于vector，随机访问的效率较低。

# 存图方法

## 数组版本

```c++
struct edge
{
	int val,to,y;
}e[N];
int head[N],tot=0;
void add(int x,int y,int v)
{
	e[++tot].val=v;
	e[tot].y=y;
	e[tot].to=head[x];
	head[x]=tot;
}
```

## vector版本

比数组版本更容易实现。

```c++
struct edge
{
	int y,v;
	edge(int Y,int V):y(Y),v(V){}
};
vector<edge> e[N];
void add(int x,int y,int v)
{
	e[x].push_back(edge(y,v));
}
```

vector版本的遍历方式只需要`for(int i=0;i<v[x].size();++i)`即可，起点是$x$，终点是$v[x][i]$。

前向星的遍历方式详见下文最短路中的$spfa$。

# 最短路

## spfa

```c++
void spfa(int s)
{
	vis[s]=1;
	memset(dis,0x3f,sizeof(dis));
	dis[s]=0;
	queue<int> q;
	q.push(s);
	while(!q.empty())
	{
		int f=q.front();
		q.pop();
		vis[f]=0;
		for(int i=head[f];i;i=e[i].to)
		{
			if(dis[e[i].y]>dis[f]+e[i].val)
			{
				dis[e[i].y]=dis[f]+e[i].val;
				if(!vis[e[i].y])
				{
					vis[e[i].y]=1;
					q.push(e[i].y);
				}
			}
		}
	}
}
```

## dij的堆优化

```c++
void dij(int s)
{
	memset(dis,0x3f,sizeof(dis));
	dis[s]=0;
	priority_queue<pii,vector<pii>,greater<pii>> q;
	q.push(mkp(0,s));
	while(!q.empty())
	{
		int x=q.top().snd;
		q.pop();
		if(vis[x]) continue;
		vis[x]=1;
		for(auto y:e[x])
		{
			if(dis[x]+y.v<dis[y.y])
			{
				dis[y.y]=dis[x]+y.v;
				q.push(mkp(dis[y.y],y.y));
			}
		}
	}
}
```

# 负环

spfa可以用于求解有没有负环，而dij不可以。

因为每完整经过负环一次最短路距离将会变得更短，理论上是可以无穷小的。

如果一个点进队大于n次，那么说明存在负环。

```c++
bool spfa(int s)
{
	queue<int> q;
	memset(dis,0x3f,sizeof(dis));
	memset(vis,0,sizeof(vis));
	memset(cnt,0,sizeof(cnt));
	dis[s]=0;
	vis[s]=cnt[s]=1;
	q.push(1);
	while(!q.empty())
	{
		int f=q.front();
		q.pop();
		vis[f]=0;
		for(int i=0;i<e[f].size();++i)
		{
			int y=e[f][i];
			if(dis[y]>dis[f]+v[f][i])
			{
				dis[y]=dis[f]+v[f][i];
				if(!vis[y])
				{
					vis[y]=1;
					q.push(y);
					cnt[y]++;
					if(cnt[y]>n) return 1;
				}
			}
		}
	}
	return 0;
}
```