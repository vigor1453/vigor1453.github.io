---
layout: post
title: "莫队算法"
date: 2019-10-11
tags: ["算法"]
comments: true
author: oneman233
excerpt: "把询问切细碎点"
---

给两道模板题：

[CF 86D](https://codeforces.com/contest/86/problem/D)

[luogu P2709](https://www.luogu.org/problem/P2709)

给出莫队的使用条件：

>   如果关于$[L,R]$的询问结果可以在$O(1)$时间内推广到$[L+1,R]$、$[L-1,R]$、$[L,R+1]$、$[L,R-1]$，那么就可以使用莫队算法在$O(n\sqrt n)$的时间内回答所有询问。
>>   本质是把所有的询问分块，最大限度地利用同一块儿中重叠的区间，尽量减少移动次数。

其实没有那么玄学，关键是边界移动时答案如何变化得想清楚，莫队只是一副骨架而已。

以下是CF例题莫队算法的部分关键代码：

1. 边界转移
    ```c++
    int now=0;
    void del(int x) {
        now-=cnt[a[x]]*cnt[a[x]]*a[x];
        cnt[a[x]]--;
        now+=cnt[a[x]]*cnt[a[x]]*a[x];
    }
    void add(int x) {
        now-=cnt[a[x]]*cnt[a[x]]*a[x];
        cnt[a[x]]++;
        now+=cnt[a[x]]*cnt[a[x]]*a[x];
    }
    ```

2. 奇偶排序
    ```c++
    bool cmp(node a,node b) {
        if(a.id==b.id) {
            if(a.id%2==1) return a.r<b.r;
            else return a.r>b.r;
        }
        else return a.id<b.id;
    }
    ```

3. 分块
    ```c++
    n=read(),m=read();
    sz=(int)sqrt(m);
    re(i,1,n) a[i]=read();
    re(i,1,m) q[i].l=read(),q[i].r=read(),q[i].ans=i;
    re(i,1,m) q[i].id=(q[i].l+sz-1)/sz;
    sort(q+1,q+1+m,cmp);
    ```

4. 回答询问
    ```c++
    int L,R;
    L=R=q[1].l;
    R--;//这样才能统计全部的答案
    re(i,1,m) {
        while(L<q[i].l) del(L++);
        while(L>q[i].l) add(--L);
        while(R>q[i].r) del(R--);
        while(R<q[i].r) add(++R);
        ans[q[i].ans]=now;
    }
    re(i,1,m) {
        write(ans[i]);
        prn();
    }
    ```

第二题的代码参见：[代码](https://www.cnblogs.com/oneman233/p/11656475.html)