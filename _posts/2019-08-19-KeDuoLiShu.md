---
layout: post
title: "珂朵莉树"
date: 2019-08-19
tags: ["旧","算法"]
comments: true
author: oneman233
excerpt: "优美的暴力"
---

基于set的究极暴力数据结构，适用于给区间赋某个值的操作，用这样的方式来尽快降低整个set当中节点的数量。

时间复杂度非常玄学。

最重要的就是split函数，用来把某个节点分成左右两部分；以及assign函数，用于给区间赋值。

初始时set不能为空，否则会出大问题。

代码：

```c++
struct node
{
	int l,r;
	mutable int v;
	node(int L,int R=-1,int V=0):l(L),r(R),v(V){}
	bool operator < (const node& o)const{return l<o.l;}
};
set<node> s;
typedef set<node>::iterator it;

it split(int pos)
{
	it i=s.lower_bound(node(pos));
	if(i!=s.end()&&i->l==pos) return i;
	--i;
	int L=i->l,R=i->r,V=i->v;
	s.erase(i);
	s.insert(node(L,pos-1,V));
	return s.insert(node(pos,R,V)).first;
}

void assign(int l,int r,int val)
{
	it ir=split(r+1),il=split(l);
	s.erase(il,ir);
	s.insert(node(l,r,val));
}

void add(int l,int r,int val)
{
	it ir=split(r+1),il=split(l);
	for(;il!=ir;il++)
		il->v+=val;
}

int rk(int l,int r,int k)
{
	vector<pair<int,int>> v;
	it ir=split(r+1),il=split(l);
	for(;il!=ir;il++)
		v.emplace_back(il->v,il->r-il->l+1);
	sort(v.begin(),v.end());
	for(int i=0;i<v.size();++i)
	{
		k-=v[i].second;
		if(k<=0) return v[i].first;
	}
	return -1; //can't find
}

int sum(int l,int r,int ex,int mod)
{
	it ir=split(r+1),il=split(l);
	int res=0;
	for(;il!=ir;il++)
		res=(res+qpow(il->v,ex,mod)*(il->r-il->l+1)%mod)%mod;
	return res;
}
```

[模板题在这儿](https://www.luogu.com.cn/problem/CF896C)