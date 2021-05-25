---
layout: post
title: "欧拉函数"
date: 2019-08-24
tags: ["算法"]
comments: true
author: oneman233
excerpt: "有很多好性质"
toc: true
---

# 定义

$phi(n)$表示$[1,n-1]$当中有几个数字与$n$互质，规定$phi(1)=1$。

# 四种求法

## 单个数的欧拉函数

```c++
int eular(int n) {
    int ans=n;
}

for(int i=2;i*i<=n;++i)
if(n%i==0) {
    ans-=ans/i;
    while(n%i==0) n/=i;
}
if(n>1) ans-=ans/n;
return ans
```

时间复杂度大概在$O(sqrt(n))$附近。

## 通项公式

如下式：

$$phi(n)=n*(1-1/p1)*...*(1-i/pn)$$

上式中$p1...pn$是$n$的所有质因数。

## 筛素数时顺便筛欧拉函数

```c++
void sieve() {
    int k=0;
    phi[1]=1;
    for(int i=2;i<=maxn;++i) {
        if(!isp[i]) {
            prime[k++]=i;
            phi[i]=i-1;
        }
        for(int j=0;j<k&&i*prime[j]<=maxn;++j) {
            isp[i*prime[j]]=1;
            if(i%prime[j]==0) {
                phi[i*prime[j]]=phi*prime[j];
                break;
            }
            else phi[i*prime[j]]=phi[i]*(prime[j]-1);
        }
    }
}
```

# 一个性质

在$[1,n-1]$范围内与$n$互质的所有正整数之和是$n/2*phi[n]$。

# 筛因子个数

```c++
int fac[maxn],low[maxn];
bool isp[maxn];
V<int> p;
void gao3() {
	re(i,2,1e6) {
		if(!isp[i])
			p.pub(i),
			fac[i]=2,
			low[i]=1;
		for(auto j:p) {
			if(j*i>maxn) break;
			isp[j*i]=1;
			if(i%j==0) {
				fac[j*i]=fac[i]/(low[i]+1)*(low[i]+2);
				low[j*i]=low[i]+1;
			}
			else
				fac[j*i]=fac[i]*fac[j],
				low[j*i]=1;
		}
	}
}
```

更多可参考[筛法](https://oi-wiki.org/math/sieve/)