---
layout: post
title: "线性基"
date: 2019-10-16
tags: ["算法"]
comments: true
author: oneman233
excerpt: "异或的基底"
---

线性基是一组数字，它从某个序列中得来，满足以下性质：

1. 异或运算的值域与原序列相同
2. 线性基中不包含0
3. 线性基任意子集的异或和不会等于0

模板如下：

```c++
/*
    p[i]数组储存的是最高位 1 在 i 这一位上的数字
    sz是线性基中数字个数
    N是线性基中数字的最高位数
    bool have(int x) 返回线性基中的数字能否表示数字x
    void ins(int x) 插入数字x
    int mn() 返回能表示的最小值
    int mx() 返回能表示的最大值
    void bug() 输出p数组
    int kth(int k) 返回能表示的所有值当中排名第k的
*/
struct LB{
	int p[100],N,tmp[100];
	bool flag;
	LB(){memset(p,0,sizeof(p));memset(tmp,0,sizeof(tmp));flag=0;N=62;}
	void ins(int x) {
		for(int i=N;i>=0;--i) {
			if(x&(1LL<<i)) {
				if(!p[i]) {p[i]=x;return;sz++;}
				else x^=p[i];
			}
		}
		flag=1;
	}
	int mx() {
		int ans=0;
		for(int i=N;i>=0;--i) {
			if((ans^p[i])>ans) ans^=p[i];
		}
		return ans;
	}
	int mn() {
		if(flag) return 0;
		for(int i=0;i<=N;++i)
			if(p[i]) return p[i];
	}
	bool have(int x) {
		for(int i=N;i>=0;--i) {
			if(x&(1LL<<i)) {
				if(!p[i]) return 0;
				else x^=p[i];
			}
		}
		return 1;
	}
	int kth(int k) {
		int res=0,cnt=0;
		k-=flag;
		if(!k) return 0;
		for(int i=0;i<=N;++i) {
			for(int j=i-1;j>=0;--j) {
				if(p[i]&(1LL<<j)) p[i]^=p[j];
			}
			if(p[i]) tmp[cnt++]=p[i];
		}
		if(k>=(1LL<<cnt)) return -1;//can't find
		for(int i=0;i<cnt;++i)
			if(k&(1LL<<i)) res^=tmp[i];
		return res;
	}
	void bug() {
		for(int i=0;i<=N;++i) cout<<p[i]<<' ';
		cout<<endl;
	}
}lb;
```

一个简单的测试正确性的主函数：

```c++
signed main() {
    lb.ins(1);
    lb.ins(2);
    lb.ins(5);
    lb.bug();
    cout<<lb.mn()<<' '<<lb.mx()<<endl;
    re(i,1,11) cout<<lb.kth(i)<<endl;
    cout<<lb.have(6)<<' '<<lb.have(8)<<endl;
    return 0;
}
```

最后放上两道模板题：

1. [luogu P3812](https://www.luogu.org/problem/P3812)
2. [CF 959F](https://codeforces.com/problemset/problem/959/F )