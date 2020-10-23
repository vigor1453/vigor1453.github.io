---
layout: post
title: "拓扑排序"
date: 2019-08-19
tags: ["旧","技术"]
comments: true
author: oneman233
excerpt: "拆除DAG"
---

在DAG上做dp的时候往往需要拓扑排序，从入度为0的点开始转移来消除dp方程的后效性：

```c++
void topo() {
    queue<int> q;
    for(int i=1;i<=n;++i)
        if(ind[i]==0) q.push(i);//入度为0
    while(!q.empty()) {
        int u=q.front();
        q.pop();
        ans[++idx]=u;//记录访问顺序
        for(auto v:edge[u]) {
            dp[v]=dp[u]+1;//转移方程
            ind[v]--;
            if(ind[v]==0) q.push(v);
        }
    }
}
```

注意有时DAG上的dp需要添加反向边，当前节点的dp值需要从反向边的终点转移过来。