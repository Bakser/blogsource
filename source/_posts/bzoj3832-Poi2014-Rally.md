title: "bzoj3832:[Poi2014]Rally"
date: 2015-03-24 07:42:33
tags: [topsort,堆]
---
神题。
<!--more-->
设f0[x]为以x为结尾的最长路的长度，f1[x]为从x开始的最长路的长度，可以在拓扑排序以后dp计算。
设一条边(u,v)的权值为f0[u]+f1[v]+1，它对应了原图的一条最长路。
假设我们将原图的点划分成了两部分，则这两部分点之间的边（割集）的权值对应了原图中的所有最长路。
所以我们只需要做到动态维护割集和割集中的最大边就可以得到删掉某个点后的答案。
假设我们按某种顺序扫过每个点，已扫过的点构成S集，未扫过的点构成T集，扫到某个点时将这个点的入边从割集中删掉，这个点的出边加入割集，用堆维护割集中的最大边权。
显然应该按照拓扑序扫描每个点，这样保证了扫到的点的入边一定在割集中。
```c++
#include<cstdio>
#include<cstring>
#include<iostream>
#include<algorithm>
#include<queue>
using namespace std;
const int N(5e5+200);
const int M(1e6+200);
struct hh{int v,next;}edges[M*2];
int n,m,en,top,ans,ansv,head1[N],head2[N],deg[N],a[N],val[M],f0[N],f1[N],sz[N];
priority_queue<int> heap;
inline void read(int &x){
	char c=0;
	for(;c<'0'||c>'9';c=getchar());
	for(x=0;c>='0'&&c<='9';c=getchar())x=x*10+(c-'0');
}
inline void addedge(int *point,int u,int v){
	edges[++en]=(hh){v,point[u]};
	point[u]=en;
}
inline void topsort(){
	static queue<int> q;
	for(int i(1);i<=n;i++)if(!deg[i])q.push(i);
	for(;!q.empty();q.pop()){
		int x(q.front());a[++top]=x;
		for(int i(head1[x]);i;i=edges[i].next)
			if(!(--deg[edges[i].v]))
				q.push(edges[i].v);
	}
}
inline void dp(){
	for(int i(1);i<=n;i++){
		int x=a[i];
		for(int j(head2[x]);j;j=edges[j].next)
			f0[x]=max(f0[edges[j].v]+1,f0[x]);
	}
	for(int i(n);i>=1;i--){
		int x(a[i]);
		for(int j(head1[x]);j;j=edges[j].next)
			f1[x]=max(f1[x],f1[edges[j].v]+1);
	}
}
inline void Ins(int x){
	if(!sz[x])heap.push(x);
	sz[x]++;
}
inline void Del(int x){
	if(sz[x])sz[x]--;
}
inline int Get(){
	while(!heap.empty()&&!sz[heap.top()])heap.pop();
	return heap.empty()?0:heap.top();
}
int main(){
	read(n);read(m);
	for(int i(0),u,v;i<m;i++){
		read(u);read(v);
		addedge(head1,u,v);deg[v]++;
		addedge(head2,v,u);
	}
	topsort();
	dp();
	for(int i(1);i<=n;i++)Ins(f1[i]);
	Ins(0);ans=1e6;
	for(int t(1);t<=n;t++){
		int x(a[t]);
		for(int i(head2[x]);i;i=edges[i].next)
			Del(f0[edges[i].v]+f1[x]+1);
		Del(f1[x]);
		if(!heap.empty()&&Get()<ans){
			ans=Get();
			ansv=x;
		}
		for(int i(head1[x]);i;i=edges[i].next)
			Ins(f0[x]+f1[edges[i].v]+1);
		Ins(f0[x]);
	}
	printf("%d %d\n",ansv,ans);
	return 0;
}
```
