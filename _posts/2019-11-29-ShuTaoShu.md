---
layout: post
title: "树套树"
date: 2019-11-29
tags: ["算法"]
comments: true
author: oneman233
excerpt: "套娃"
---

维护带修的区间第$k$小，普通的主席树已经不能满足要求。

但是仍然需要用到主席树的思想，用树状数组维护下标，每一个节点都是一棵值域线段树。

在值域离散化之后，只需要选出$log$棵线段树进行$log$次前缀和即可找到第$k$大。

数组往大了开，不然都不知道为什么$WA$。

例题是[luogu P2617](https://www.luogu.com.cn/problem/P2617)。

代码如下：

```c++
#include <bits/stdc++.h>
#define int long long
#define sci(a) scanf("%lld",&a)
#define scii(a,b) scanf("%lld%lld",&a,&b)
#define sciii(a,b,c) scanf("%lld%lld%lld",&a,&b,&c)
#define scc(a) scanf("%c",&a)
#define scs(a) scanf("%s",a)
#define pri(a) printf("%lld",a)
#define prc(a) printf("%c",a)
#define prs() printf(" ")
#define prn() printf("\n")
#define re(i,a,b) for(int i=a;i<=b;++i)
#define fo(i,a,b) for(int i=a;i<b;++i)
#define rre(i,a,b) for(int i=a;i>=b;--i)
#define each(i,a) for(auto i=a.begin();i!=a.end();++i)
#define mkp make_pair
#define fst first
#define snd second
#define frt front
#define bak back
#define pub(a) push_back(a)
#define pob() pop_back
#define puf(a) push_front(a)
#define pof() pop_front
#define mem0(a) memset(a,0,sizeof(a))
#define memmx(a) memset(a,0x3f3f,sizeof(a))
#define memmn(a) memset(a,-0x3f3f,sizeof(a))
#define nmsl cout<<"NMSL"<<endl
#define Yes cout<<"Yes"<<endl
#define No cout<<"No"<<endl
#define FAST ios_base::sync_with_stdio(0);cin.tie(0),cout.tie(0);
#define endl '\n'
using namespace std;
typedef long double db;
typedef pair<int,int> pii;
typedef pair<db,int> pdi;
typedef vector<int> vei;
typedef vector<pii> vep;
typedef vector<char> vec;
typedef vector<string> ves;
typedef map<int,int> mpii;
typedef map<pii,int> mppi;
typedef map<char,int> mpci;
typedef map<string,int> mpsi;
typedef deque<int> dqi;
typedef deque<pii> dqp;
typedef deque<char> dqc;
typedef deque<string> dqs;
typedef priority_queue<int> mxpi;
typedef priority_queue<int,vei,greater<int>> mnpi;
typedef priority_queue<pii> mxpp;
typedef priority_queue<pii,vep,greater<pii>> mnpp;
const int maxn=20000005;
const int inf=0x3f3f3f3f3f3f3f3f;
const db eps=1e-10;
const db pi=3.1415926535;
int lowb(int x){return x&-x;}
int mmax(int a,int b,int c){return max(max(a,b),c);}
int mmin(int a,int b,int c){return min(min(a,b),c);}
int count2(int x){return __builtin_popcountll(x);}
int ll(int p){return p<<1;}
int rr(int p){return p<<1|1;}
int mm(int l,int r){return (l+r)/2;}
int lg(int x){if(x==0) return 1;return (int)log2(x)+1;}
bool smleql(db a,db b){if(a<b||fabs(a-b)<=eps)return true;return false;}
bool bigeql(db a,db b){if(a>b||fabs(a-b)<=eps)return true;return false;}
bool eql(db a,db b){if(fabs(a-b)<eps) return 1;return 0;}
db len(db a,db b,db c,db d){return sqrt((a-c)*(a-c)+(b-d)*(b-d));}
int gcd(int x,int y){return __gcd(x,y);}
int lcm(int x,int y){return x*y/__gcd(x,y);}
bool isp(int x){if(x==1)return false;if(x==2)return true;for(int i=2;i*i<=x;++i)if(x%i==0)return false;return true;}
int qpow(int a,int b,int mod){int tmp=a%mod,ans=1;while(b){if(b&1) ans=(ans*tmp)%mod;tmp=(tmp*tmp)%mod;b>>=1;}return ans;}
inline int read(){char ch=getchar();int s=0,w=1;while(ch<48||ch>57){if(ch=='-')w=-1;ch=getchar();}while(ch>=48&&ch<=57){s=(s<<1)+(s<<3)+ch-48;ch=getchar();}return s*w;}
inline void write(int x){if(x<0)putchar('-'),x=-x;if(x>9)write(x/10);putchar(x%10+48);}

int n,m,a[maxn];
struct OP
{
	int tp;
	int l,r,k,x,y;
}op[maxn];
struct SEG
{
	int v,ls,rs;
}seg[maxn];
vei con;
int tot=0,rt[maxn];
int cnt[2],tmp[2][30];

void modi2(int &p,int l,int r,int pos,int val)
{
	if(!p) p=++tot;
	seg[p].v+=val;
	if(l==r) return;
	int m=mm(l,r);
	if(pos<=m) modi2(seg[p].ls,l,m,pos,val);
	else modi2(seg[p].rs,m+1,r,pos,val);
}

void modi1(int x,int val)
{
	int k=lower_bound(con.begin(),con.end(),a[x])-con.begin()+1;
	for(int i=x;i<=n;i+=lowb(i))
		modi2(rt[i],1,con.size(),k,val);
}

int ask2(int l,int r,int k)
{
	if(l==r) return l;
	int m=mm(l,r),sum=0;
	re(i,1,cnt[1]) sum+=seg[seg[tmp[1][i]].ls].v;
	re(i,1,cnt[0]) sum-=seg[seg[tmp[0][i]].ls].v;
	if(k<=sum)
	{
		re(i,1,cnt[1]) tmp[1][i]=seg[tmp[1][i]].ls;
		re(i,1,cnt[0]) tmp[0][i]=seg[tmp[0][i]].ls;
		return ask2(l,m,k);
	}
	else
	{
		re(i,1,cnt[1]) tmp[1][i]=seg[tmp[1][i]].rs;
		re(i,1,cnt[0]) tmp[0][i]=seg[tmp[0][i]].rs;
		return ask2(m+1,r,k-sum);
	}
}

int ask1(int l,int r,int k)
{
	cnt[1]=cnt[0]=0;
	mem0(tmp);
	for(int i=r;i;i-=lowb(i)) tmp[1][++cnt[1]]=rt[i];
	for(int i=l-1;i;i-=lowb(i)) tmp[0][++cnt[0]]=rt[i];
	return ask2(1,con.size(),k);
}

signed main()
{
    FAST
    cin>>n>>m;
    re(i,1,n) cin>>a[i],con.pub(a[i]);
    re(i,1,m)
    {
		char ch;
		cin>>ch;
		if(ch=='Q')
		{
			op[i].tp=1;
			cin>>op[i].l>>op[i].r>>op[i].k;
		}
		else if(ch=='C')
		{
			op[i].tp=2;
			cin>>op[i].x>>op[i].y;
			con.pub(op[i].y);
		}
	}
	sort(con.begin(),con.end());
	re(i,1,n) modi1(i,1);
	re(i,1,m)
	{
		if(op[i].tp==1)
		{
			cout<<con[ask1(op[i].l,op[i].r,op[i].k)-1]<<endl;
		}
		else if(op[i].tp==2)
		{
			modi1(op[i].x,-1);
			a[op[i].x]=op[i].y;
			modi1(op[i].x,1);
		}
	}
    return 0;
}
```