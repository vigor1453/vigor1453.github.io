---
layout: post
title: "关于倍增ST表"
date: 2019-08-18
tags: ["旧","算法"]
comments: true
author: oneman233
excerpt: "我的最小值等于左边最小值和右边最小值中的最小值"
---

查询静态区间最值，$O(nlogn)$预处理，接近$O(1)$内完成查询。预处理代码：

```c++
//dp[i][j]表示从a[i]开始，包括a[i]在内的2的j次方个数字中的最值
for(int i=1;i<=n;++i)
	dp[i][0]=a[i];
for(int j=1;j<=30;++j){
	for(int i=1;i+(1LL<<(j-1))<=n;++i){
		dp[i][j]=max(dp[i][j-1],dp[i+(1LL<<(j-1))][j-1]);//min
	}
}
```

本质上是由小区间的结果合并出大区间的结果。

查询代码：

```c++
int ask(int l,int r) {
    int k=(int)log2(r-l+1);
    return max(dp[l][k],dp[r-(1<<k)+1][k]);//可以把max改成min
}
```

从左端点向右找和从右端点向左找保证了覆盖到整个区间。