---
layout: post
title: "LCA两种搞法"
date: 2019-08-18
tags: ["旧","技术"]
comments: true
author: oneman233
excerpt: "找爸爸"
toc: true
---

# 倍增

基本原理是把一个十进制数转化为二进制存储，如$10=8+2=1010$，原本把10减小到0需要十次，现在只需四次即可。

为此需要设定函数$lg(x)$，使得$2^{lg(x)}>=x$，便于二进制分解。

对一棵树找lca的朴素做法是两者同时向上找父亲，直到相等为止（首先需要令两者深度相等），同一棵树中某个节点的父亲节点是唯一的，这样做一定可以使得两者在某一时刻相等，即找到了lca。

倍增则希望把向上找的一层一层上跳变成一次上跳$2^n$层。

先进行一些预处理：

```c++
/*
    dep是节点深度
    fa[x][i]是x节点的第2的i次方（即是(1<<i)）辈祖先的编号
*/
void dfs(int x,int f)
{
	dep[x]=dep[f]+1;
	fa[x][0]=f;
	for(int i=1;(1<<i)<=dep[x];++i)
		fa[x][i]=fa[fa[x][i-1]][i-1];
	for(auto y:e[x])
	{
		if(y==f) continue;
		dfs(y,x);
	}
}
```

显而易见的是：$x$的$2^{i-1}$祖先的$2^{i-1}$祖先是x的$2^i$祖先，此处可参考ST表的原理。

注意一点：**x的祖先数不可能超过它的深度。**

随后是lca的设计：

```c++
int lg(int x){return (int)log2(x)+1;}
int lca(int x,int y)
{
    if(dep[x]<dep[y]) swap(x,y);
    if(dep[x]!=dep[y]){
        int dif=dep[x]-dep[y];
        for(int i=0;i<lg(dif);++i)
            if(dif&(1<<i))
                x=fa[x][i];
    }
    if(x==y)
        return x;
    for(int i=lg(dep[x])-1;i>=0;--i){
        if(fa[x][i]!=fa[y][i]){
            x=fa[x][i];
            y=fa[y][i];
        }
    }
    return fa[x][0];
}
```

# tarjan离线

一共有六个步骤，借助于并查集：

1. 任选一个root
2. 遍历与root相连的所有节点并标记为已访问
3. 若root还有未访问的子节点，继续b操作
4. 合并所有的子节点到root上
5. 寻找询问当中与root有关系的点p
6. 若p已经被访问了，那么root与p的lca为p的祖先节点，否则目前还不能求出root与p的lca

代码实现如下：

```c++
void tarjan(int u)
{
	vis[u]=1;
	for(auto v:e[u])
	{
		if(!vis[v])
		{
			tarjan(v);
			_merge(v,u);
		}
	}
	for(int i=0;i<q[u].size();++i)
	{
		int v=q[u][i];
		int k=id[u][i];
		if(vis[v]&&ans[k]==0)
			ans[k]=_find(v);
	}
}
```