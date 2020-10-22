---
layout: post
title: "割点和强连通分量"
date: 2019-08-18
tags: ["旧","技术"]
comments: true
author: oneman233
excerpt: "如何坍缩"
toc: true
---

# tarjan求割点（无向图）

定理：

    割边一定依附于割点，而有割点不一定有割边

同时给出定义：

    在一次dfs当中，由u访问到v，称u为v的父亲节点，v为u的孩子节点，u之前被访问到的是v的祖先节点

割点的判定方法：

    若u的全部孩子可以不通过u来访问祖先节点，那么u不是割点；
    而如果至少存在一个这样的孩子，u就是那个孩子与祖先节点的割点

具体实现：

```c++
/*
    dep[i] 遍历顺序的时间戳，子节点的时间戳一定大于父节点，访问一次节点后其时间戳就被确定了
    low[i] i节点不通过父节点就能访问到的最小时间戳，初始low[i]=dep[i]
    从任意顶点出发都可以完成tarjan
*/
void tarjan(int u,int f)
{
	dfn[u]=low[u]=++timer;
	int son=0;
	for(auto v:e[u])
	{
		if(dfn[v]==0)
		{
			tarjan(v,u);
			low[u]=min(low[u],low[v]);
			if(low[v]>=dfn[u]&&u!=f) is[u]=1;
			if(u==f) son++;
		}
		low[u]=min(low[u],dfn[v]);
	}
	if(son>=2&&u==f) is[u]=1;//point on the top
}
```

在上面的函数中，可以使用下面的形式：

    tarjan(i,i)

在某些不一定联通的图中可以有效处理根节点是割点的情况

# tarjan求桥（无向图）

定理：

    对于所有的u的子节点v：
    low[v]>=dep[u] u是割点
    low[v]>dep[u]  u-v是桥

具体实现：

```c++
void tarjan(int u,int f)
{
	fa[u]=f;
	dfn[u]=low[u]=++timer;
	for(auto v:e[u])
	{
		if(!dfn[v])
		{
			tarjan(v,u);
			low[u]=min(low[u],low[v]);
			//if(dfn[u]<low[v]) is[u][v]=1;
			//u is v's father
		}
		else if(v!=f) low[u]=min(low[u],dfn[v]);
	}
}
```

# tarjan求强连通分量（有向图）

具体实现：

```c++
/*
    scc表示某标号的强连通分量中的点
    co表示某个点属于哪个强连通分量
*/
void tarjan(int u)
{
	dfn[u]=low[u]=++timer;
	s.push(u);
	vis[u]=1;
	for(auto v:e[u])
	{
		if(!dfn[v])
		{
			tarjan(v);
			low[u]=min(low[u],low[v]);
		}
		else if(vis[v]) low[u]=min(low[u],dfn[v]);
	}
	if(low[u]==dfn[u])
	{
		++color;
		int t;
		do
		{
			t=s.top();
			s.pop();
			co[t]=color;
			vis[t]=0;
			scc[color].push_back(t);
		}
		while(u!=t);
	}
}
```

# tarjan求割点的一个拓展（无向图）

为了记录割点所分开的各联通块分别的点数，可以在这样修改tarjan：

```c++
/*
    此时part[u]当中储存的就是u号割点分开的各个联通块的个数
    需要注意的是它只能用于连通图，并且使用tarjan(1,0)
    它不能处理根节点是割点的情况
*/
void tarjan(int u,int f)
{
	dfn[u]=low[u]=++timer;
	sz[u]++;//
	int son=0,tmp=0;
	for(auto v:e[u])
	{
		if(dfn[v]==0)
		{
			tarjan(v,u);
			sz[u]+=sz[v];//
			low[u]=min(low[u],low[v]);
			if(low[v]>=dfn[u]&&u!=f)
			{
				is[u]=1;
				tmp+=sz[v];//
				part[u].push_back(sz[v]);//
			}
			if(u==f) son++;
		}
		low[u]=min(low[u],dfn[v]);
	}
	if(son>=2&&u==f) is[u]=1;//point on the top
	if(is[u]&&n-tmp-1!=0)
		part[u].push_back(n-tmp-1);//
}
```

有注释的部分是相对于求割点增加的代码，**注意这种情况很容易爆int**

