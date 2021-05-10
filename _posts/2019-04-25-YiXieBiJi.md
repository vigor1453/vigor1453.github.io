---
layout: post
title: "一些笔记"
date: 2019-04-25
tags: ["技术"]
comments: true
author: oneman233
excerpt: "琐碎的tips"
toc: true
---

# 在线代码高亮

[地址](http://www.planetb.ca/syntax-highlight-word)

# 键盘锁定
    
    sc config i8042prt start= disabled

# U盘出现的一个问题

    错误0x800700ea:有更多数据可用

U盘的文件系统是FAT32，将U盘的文件系统格式化为NTFS即可解决(注意提前备份好原有文件）。

# 板子：

编译命令：

```
-std=c++11
```

```c++
#include <bits/stdc++.h>
#define int long long
#define re(i,a,b) for(int i=a;i<=b;++i)
#define fo(i,a,b) for(int i=a;i<b;++i)
#define rre(i,a,b) for(int i=a;i>=b;--i)
#define all(a) a.begin(),a.end()
#define F first
#define S second
#define pub push_back
#define pob pop_back
#define puf push_front
#define pof pop_front
#define nmsl cout<<"NMSL\n"
#define yes cout<<"Yes\n"
#define no cout<<"No\n"
#define FAST ios_base::sync_with_stdio(0);cin.tie(0),cout.tie(0);
#define endl '\n'
#define P pair
#define V vector
using namespace std;
typedef priority_queue<int> mxq_i;
typedef priority_queue<int,V<int>,greater<int> > mnq_i;
typedef priority_queue<P<int,int> > mxq_p;
typedef priority_queue<P<int,int>,V<P<int,int> >,greater<P<int,int> > > mnq_p;
int read(){char ch=getchar();int s=0,w=1;while(ch<48||ch>57){if(ch=='-')w=-1;ch=getchar();}while(ch>=48&&ch<=57){s=(s<<1)+(s<<3)+ch-48;ch=getchar();}return s*w;}
void write(int x){if(x<0)putchar('-'),x=-x;if(x>9)write(x/10);putchar(x%10+48);}

const int maxn=1000005;
const int inf=0x3f3f3f3f3f3f3f3f;

signed main() {
    FAST

    return 0;
}
```

# 如何开启笔记本的蓝牙

win10蓝牙在开始页面的齿轮处选择设备即可开启

# 尽量转成pdf再交付打印

# wps调整行间距的方法

<div align=center>
    <img src="../images/2019-04-25-YiXieBiJi-1.png"/>
    <p style="font-size:14px;color:#C0C0C0;text-decoration:underline">
        如何调整行间距
    </p>
</div>

# wps一些工具

| 作用 | 操作方法 |
|----|----|
|插入横线|插入-形状-线条|
|打开网格线|视图-网格线（用于处理文档对齐）|
|打勾|插入-符号-√|
|在行首插入项目标记（就是一个原点或者序号）|右键-插入项目符号和编号|

# 一些有用的网站

|作用|网址|
|----|----|
|pdf转word| [网址](https://smallpdf.com/cn/pdf-to-word) |
|在线代码高亮| [网址](http://www.planetb.ca/syntax-highlight-word) |
|在线Latex| [网址](https://zh.numberempire.com/latexequationeditor.php) |

**注意代码高亮复制出来的时候要带格式粘贴**

# WPS转PDF报未定义书签的错

新建的书签要右键更新域才可使用

# 一些系统快捷键

| 快捷键 | 作用 |
| ---- | ---- |
| ctrl+z | 撤销 |
| ctrl+y | 恢复 |
| ctrl+x | 剪切 |

# Casio如何转换进制

1. 菜单选择基数
2. shift加上log ln等按键转换进制
3. 输入时用十进制，按下=后再获得结果

# C语言printf的对齐方法

[网址](https://blog.csdn.net/abcdu1/article/details/74926375/)