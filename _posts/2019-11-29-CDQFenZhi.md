---
layout: post
title: "cdq分治"
date: 2019-11-29
tags: ["算法"]
comments: true
author: oneman233
excerpt: "感谢陈丹琦"
---

cdq分治并不是一种算法，而是某种思想。

**把左区间的修改统计到右区间的询问上去**，类似于归并排序。

很多时候可以用于解决一些离线的问题，使问题简化不少。

有两个例题，都是经典的cdq解决三维偏序：

1. 可重复的三维偏序

```c++
#include <bits/stdc++.h>
using namespace std;

/*
    luogu P3810
    解决有等于的三维偏序
    严格小于等于的个数，可以解决重复问题，有离散化
*/

const int maxn=500005;

int n,k;
int cnt[maxn];//save the ans
struct ss{
	int a,b,c,w,ans;
}tmps[maxn],s[maxn];//struct
bool cmp1(ss x,ss y){//sort1
	if(x.a==y.a){
		if(x.b!=y.b) return x.b<y.b;
		else return x.c<y.c;
	}
	else return x.a<y.a;
}
bool cmp2(ss x,ss y){//sort2
	if(x.b!=y.b) return x.b<y.b;
	else return x.c<y.c;
}

struct tree_array{//tree_array
	int tr[maxn+5],n;
	int lowbit(int x){return x&-x;}
	int ask(int x){int ans=0;for(;x;x-=lowbit(x))ans+=tr[x];return ans;}
	void add(int x,int y){for(;x<=n;x+=lowbit(x))tr[x]+=y;}	
}t;

void cdq(int l,int r){
	if(l==r) return;
	int m=l+r>>1;
	cdq(l,m);
	cdq(m+1,r);
	sort(s+l,s+m+1,cmp2);
	sort(s+m+1,s+r+1,cmp2);//sort2
	int i=l,j=m+1;
	for(;j<=r;++j){
		while(i<=m&&s[i].b<=s[j].b){//the second dimension
			t.add(s[i].c,s[i].w);//use the tree_array to save the ans
			++i;
		}
		s[j].ans+=t.ask(s[j].c);//contribution
	}
	for(int j=l;j<i;++j)
		t.add(s[j].c,-s[j].w);//init the first half
}

int main(){
	scanf("%d%d",&n,&k);
	for(int i=1;i<=n;++i)
		scanf("%d%d%d",&tmps[i].a,&tmps[i].b,&tmps[i].c);
	sort(tmps+1,tmps+1+n,cmp1);//sort1
	int now=0,nn=0;
	for(int i=1;i<=n;++i){
		now++;
		if(tmps[i].a!=tmps[i+1].a||tmps[i].b!=tmps[i+1].b
		||tmps[i].c!=tmps[i+1].c){
			s[++nn]=tmps[i];
			s[nn].w=now;
			now=0;
		}//compress the same
	}
	t.n=maxn;//tree_array on the range
	cdq(1,nn);
	for(int i=1;i<=nn;++i)
		cnt[s[i].ans+s[i].w-1]+=s[i].w;//
	for(int i=0;i<n;++i)
		printf("%d\n",cnt[i]);
	return 0;
}
```

2. 严格不重复的三维偏序（注意排序方法的不同）

```c++
#include <bits/stdc++.h>
using namespace std;

/*
    CF 12D
    严格大于的三维偏序
    无法处理重复的数字，但是注意"大于"的神秘排序方法
*/

const int N=500005;

int n,c[N];
struct node{
	int a,b,c,cnt;
}o[N],t[N];

bool cmp(node a,node b){
	if(a.a!=b.a) return a.a>b.a;
	else return a.c<b.c;
}

inline int lowbit(int x){return x&-x;}
int tr[N];
void add(int x,int y){
	for(;x<=n;x+=lowbit(x)) tr[x]+=y;
}
int sum(int x){
	int res=0;
	for(;x;x-=lowbit(x)) res+=tr[x];
	return res;
}

void cdq(int l,int r){
	if(l==r) return;
	int m=(l+r)/2;
	cdq(l,m);
	cdq(m+1,r);
	int p=l,q=m+1,tot=l;
	while(p<=m&&q<=r){
		if(o[p].b>o[q].b) add(o[p].c,1),t[tot++]=o[p++];
		else o[q].cnt+=sum(n)-sum(o[q].c),t[tot++]=o[q++];
	}
	while(p<=m) add(o[p].c,1),t[tot++]=o[p++];
	while(q<=r) o[q].cnt+=sum(n)-sum(o[q].c),t[tot++]=o[q++];
	for(int i=l;i<=m;++i) add(o[i].c,-1);
	for(int i=l;i<=r;++i) o[i]=t[i];
}

int main()
{
	scanf("%d",&n);
	for(int i=1;i<=n;++i) scanf("%d",&o[i].a);
	for(int i=1;i<=n;++i) scanf("%d",&o[i].b);
	for(int i=1;i<=n;++i) scanf("%d",&o[i].c),c[i]=o[i].c;
	sort(c+1,c+1+n);
	for(int i=1;i<=n;++i) o[i].c=lower_bound(c+1,c+1+n,o[i].c)-c;
	sort(o+1,o+1+n,cmp);
	cdq(1,n);
	int ans=0;
	for(int i=1;i<=n;++i) if(o[i].cnt>0) ans++;
	printf("%d",ans);
	return 0;
}
```