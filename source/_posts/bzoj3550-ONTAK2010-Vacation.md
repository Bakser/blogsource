title: "bzoj3550:[ONTAK2010]Vacation"
date: 2015-03-23 15:52:40
tags: [线性规划,费用流]
---
线性规划问题转网络流。
<!--more-->
设$a\_{i}$为第i个数,$x\_{i}$为第i个数选或不选($0\leq x\_{i} \leq 1$)
$Maximize\sum\_{i=1}^{3n}a\_{i}x\_{i}\\\Subject\ to\\\\\forall  z\ \epsilon [1,2n] \sum\_{i=z}^{z+n-1}x\_{i}\leq k\\\\\forall i\  \epsilon[1,3n]0\leq x\_{i} \ \leq 1$
<del>上单纯形</del>
添加松弛变量$y\_{z}$
$\forall  z\ \epsilon [1,2n] \sum\_{i=z}^{z+n-1}x\_{i}+y\_{z}=k,y\_{z}>=0$
对等式差分以后得到
$\sum\_{i=1}^{n}x\_{i}+y\_{1}=k\\\\\forall z\ \epsilon [1,2n-1]\ x\_{z+n}-x\_{z}+y\_{z+1}-y\_{z}=0\\\ \\sum\_{i=2n+1}^{3n}x\_{i}-y\_{2n+1}=-k$
采用跟志愿者招募一样的建图方式。
>添加虚拟源汇S和T，把每个等式建成一个点。
等式右面常数项为k>0则从S向这个点连一条容量为k，费用为0的边。若常数项为k<0,从这个点向T连一条容量为k，费用为0的边。
如果一个变量$x\_{i}$在第j个等式中出现为$x\_{i}$，在第k个等式中出现为$-x\_{i}$，从顶点j向顶点k连接一条容量为1，费用为$a\_{i}$的有向边。
如果一个变量$y\_{i}$在第j个等式中出现为$y\_{i}$，在第k个等式中出现为$-y\_{i}$，从顶点j向顶点k连接一条容量为∞，费用为0的有向边。


```c++
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<iostream>
#include<climits>
#include<queue>
using namespace std;
const int N(1000);
const int INF(0x3f3f3f3f);
struct hh{
	int u,v,cap,cost,next;
}edges[2*N*N];
int n,k,s,t,mxflow,mxcost,a[N],point[N],en=-1,d[N],p[N],mn;
bool in[N];
inline void addedge(int u,int v,int cap,int cost){
	edges[++en]=(hh){u,v,cap,cost,point[u]};
	point[u]=en;
}
inline void Add(int u,int v,int cap,int cost){
	addedge(u,v,cap,cost);
	addedge(v,u,0,-cost);
}
inline bool spfa(){
	memset(d,0xaf,sizeof(d));mn=d[0];
	d[s]=p[s]=0;in[s]=1;
	static queue<int> q;
	for(q.push(s);!q.empty();q.pop()){
		int x(q.front());in[x]=0;
		for(int i(point[x]),v;i!=-1;i=edges[i].next)
				if(edges[i].cap&&d[v=edges[i].v]<d[x]+edges[i].cost){
				d[v]=d[x]+edges[i].cost;
				p[v]=i;
				if(!in[v])q.push(v),in[v]=1;
			}
	}
	if(d[t]==mn)return 0;
	int flow=INF;
	for(int x(t);x!=s;x=edges[p[x]].u)flow=min(flow,edges[p[x]].cap);
	for(int x(t);x!=s;x=edges[p[x]].u)edges[p[x]].cap-=flow,edges[p[x]^1].cap+=flow;
	mxflow+=flow;mxcost+=flow*d[t];
	return 1;
}
int main(){
	scanf("%d%d",&n,&k);
	memset(point,0xff,sizeof(point));
	s=0;t=3*n+1;
	for(int i(1);i<=n*3;i++)scanf("%d",a+i);
	Add(s,1,k,0);Add(2*n+2,t,k,0);
	for(int i(1);i<=n;i++)Add(1,i+1,1,a[i]);
	for(int i(n+1);i<=2*n;i++)Add(i-n+1,i+1,1,a[i]);
	for(int i(2*n+1);i<=3*n;i++)Add(i-n+1,2*n+2,1,a[i]);
	for(int i(1);i<=2*n+1;i++)Add(i,i+1,INF,0);
	while(spfa());
	printf("%d\n",mxcost);
	return 0;
}
```
