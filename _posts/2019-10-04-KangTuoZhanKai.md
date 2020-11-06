---
layout: post
title: "康托展开"
date: 2019-09-19
tags: ["旧","算法"]
comments: true
author: oneman233
excerpt: "全排列的字典序"
---

一种神秘的哈希方法，**可以用于求解某个全排列的字典序，也可以用逆康拓展开求每个字典序位置上的全排列**。

模板题有四个：

1. [入门](https://www.luogu.org/problem/P5367)
2. [简单](https://www.luogu.org/problem/P3014)
3. [中等](https://www.luogu.org/problem/UVA11525)
4. [困难](https://codeforces.com/problemset/problem/501/D)

实际上也没有那么模板，还是得看懂怎么构造出来的。

首先给出公式：

$$
rank=a_i*(n-1)!+a_{i-1}*(n-2)!+...+a_1*(0)!
$$

其中$a_i$表示**在$i$前面并且值小于$i$位置上值的数字个数**。

注意**该排名是从$0$开始的**，返回值的时候记得加$1$。

不妨考虑以下全排列：

$$
\left\{
\begin{matrix}
    2 & 3 & 4 & 1
\end{matrix}
\right\}
$$

整个计算过程如下：

1. 第一位为$2$，那么以$1$开头的所有排列位于该排列前，共有$1*3！$种
2. 第二位为$3$，那么以$1$或者$2$为第二位的所有排列位于该排列前，但是计算此处时要求第一位相同，所以只有$1$对答案产生了贡献，共有$1*2！$种
3. 第三位为$4$，那么以$1$、$2$或$3$为第三位的所有排列位于该排列前，但是计算此处时要求第一位和第二位相同，所以只有$1$对答案产生了贡献，共有$1*1！$种
4. 第四位不会产生贡献

实际上类似于**一种以$n！$为基底的进制**。

根据以上结论可以把$O(n^2)$的代码搞出来：

```c++
int contor(vector& p) {
    int ans=0;
    for(int i=0;i<p.size();++i) {
        int cnt=0;
        for(int j=i+1;j<p.size();++j)
            if(p[j]<p[i])
                ++cnt;
        ans=(ans+cnt*fac[p.size()-i-1]%MOD)%MOD;
    }
    return ans+1;
}
```

这份代码交洛谷康托展开模板题只能得一半的分，复杂度太吃瘪了。

实际上用$O(n^2)$来**找这个数后面有几个数比他小**实在是太不优美了，不如换成权值树状数组。

遍历的时候**倒着向前往树状数组里$add$**，$sum$的时候求有多少个值小于当前值即可。

实际上也可以推出：

>   $a_i$后面比$a_i$小的数的个数=总共比$a_i$小的数的个数−$a_i$前面比$a_i$小的数的个数

这样就可以在树状数组中正序求解。

这样就可以通过洛谷的模板题：

```c++
//contor展开
int bit[maxn];
void add(int x,int y) {
     for(;x<=n;x+=lowbit(x)) bit[x]+=y; } 
int sum(int x) {
int res=0;
for(;x>0;x-=lowbit(x)) res+=bit[x];
    return res;
}
int contor(vector& p) {
    int ans=0;
    for(int i=p.size()-1;i>=0;--i) {
        add(p[i],1);
        int cnt=sum(p[i]-1);
        ans=(ans+cnt*fac[p.size()-i-1]%MOD)%MOD;
    }
    return ans+1;
}
```

至于第二道题，需要求**某个排列的字典序和某个字典序位置上的排列**，就需要用到逆康托展开了。

主要有三个步骤：

1. 首先把当前排名减一以获得排在它前面的排列个数
2. 然后按照进制数分解的方式找到$a_i$
3. 这一位上的数字就是未使用的第$a_i$小数字（数字排名从$0$开始，因为该位上数字可能为$0$）

代码如下：

```c++
#define vei vector<int>
#define fo(i,a,b) for(int i=a;i<b;++i)
#define re(i,a,b) for(int i=a;i<=b;++i)
vei recontor(int len,int ans) {
    --ans;
    vei p(len,-1);
    vector vis(len+1,0);// 记录某数字是否被使用
    int n=len;
    fo(i,0,len) {
        int t=ans/fac[len-i-1];
        ans%=fac[len-i-1];
        re(j,1,len) {
            if(!vis[j]&&!(t--)) {// 找第t小的数字
                p[i]=j;
                vis[j]=1;
            }
        }
    }
    return p;
}
```

第三题题面其实就是一个康托展开，但是会卡输出格式。

需要用到快速的逆康托展开，也就是**快速找到未使用的第$k$小数字**。

可以考虑**用值域线段树维护区间排名，每次取出一个数字就把这个数字上的值置零**。

代码如下：

```c++
/*
    为了符合题面
    代码中s[i]是逆康托展开的a[i]
    k是逆康托展开的n
*/
#define vei vector<int>
#define fo(i,a,b) for(int i=a;i<b;++i)
#define re(i,a,b) for(int i=a;i<=b;++i)
#define ll(a) a<<1;
#define rr(a) a<<1|1;
#define mm(a,b) (a+b)/2;
int k,s[50005];
int tr[200005];
void up(int p){tr[p]=tr[ll(p)]+tr[rr(p)];}
void build(int p=1,int l=1,int r=k) {
    if(l==r) {
        tr[p]=1;
        return;
    }
    int m=mm(l,r);
    build(ll(p),l,m);
    build(rr(p),m+1,r);
    up(p);
}
int ask(int cnt,int p=1,int l=1,int r=k) {
    if(l==r) {
        tr[p]=0;
        return l;
    }
    int m=mm(l,r);
    int ans=-1;
    if(cnt<=tr[ll(p)]) ans=ask(cnt,ll(p),l,m);
    else ans=ask(cnt-tr[ll(p)],rr(p),m+1,r);
    up(p);
    return ans;
}
vei recontor(){
    vei v(k+1);
    re(i,1,k) v[i]=ask(s[i]+1);
    return v;
}
```

第四题要更麻烦一点，因为有个非常不好想的取余。

题目需要求出两个长度相同的排列的字典序，求和，再对长度的阶乘取余，最后求出取余结果位置上的排列。

下面这个公式可以用于处理**以阶乘为基底的高精度数的取模问题**，保证每一位上的数字不超过基底：

$$
\begin{aligned}
    & \sum_{i=1}^n S_i*(n-i)! \ mod \ P \\
    & \because S_{i+1}*(n-i-1)!=\cfrac{S_{i+1}*(n-i)!}{n-i} \\
    & \therefore S_i*(n-i)!+S_{i+1}*(n-i-1)!=(S_i+\cfrac{S_{i+1}}{n-i})*(n-i)! \\
\end{aligned}
$$

代码如下：

```c++
/*
    a中保存的是最后结果，tmp保存的是进位
    a的低位实际上是进制数的高位，所以最后要把a数组全部倒过来
*/
#define re(i,a,b) for(int i=a;i<=b;++i)
int tmp=0;
re(i,2,n) {
    a[i]+=b[i]+tmp;
    tmp=a[i]/(i+1);
    a[i]-=tmp*(i+1);
}
reverse(a,a+n);
```