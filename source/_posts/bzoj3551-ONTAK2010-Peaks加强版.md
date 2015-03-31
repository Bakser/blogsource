title: "bzoj3551:[ONTAK2010]Peaks加强版"
date: 2015-03-27 14:21:31
tags: [krusal重构树,主席树]
---
开小数组一时爽
<!--more-->
krusal的时候加入边u、v时建立一个虚点，从这个虚点向find(u)、find(v)连边，这个虚点的点权为(u,v)边权。然后这重构出来的树有奇异的性质，肯定是大根堆，然后询问的每个点倍增往上找第一个小于询问边权的虚点，它的子树里求k大就好了。
```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <iostream>
using namespace std;
const int N(2e5+200);
const int M(5e5+200);
const int D(18);
struct edge{
	int u,v,next,w;
	inline bool operator <(const edge &b)const{return w<b.w;}
}e[M],edges[N*2];
struct hh{
	hh *l,*r;
	int sz;
}*Null,*rot[N*D],tree[N*D],*tail=tree;
int n,m,nn,en,dfn,ans,Q,h[N],val[N],ls[N],seq[N],lsnum,fa[N],f[N][D+1],mx[N][D+1],point[N],in[N],out[N];
inline void read(int &x){
	char c=0;int f=1;
	for(;c<'0'||c>'9';c=getchar())if(c=='-')f=-1;
	for(x=0;c>='0'&&c<='9';c=getchar())x=x*10+(c-'0');
	x*=f;
}
inline hh* newnode(){
	hh *x=++tail;
	x->l=x->r=Null;x->sz=0;
	return x;
}
inline void addedge(int u,int v){
	edges[++en]=(edge){u,v,point[u],0};
	point[u]=en;
}
inline int  find(int x){
	return fa[x]==x?x:fa[x]=find(fa[x]);
}
inline void krusal(){
	sort(e+1,e+m+1);
	for(int i(1);i<=n*2;i++)fa[i]=i;
	for(int i(1);i<=m;i++){
		int u=find(e[i].u),v=find(e[i].v);
		if(u==v)continue;
		fa[u]=fa[v]=f[u][0]=f[v][0]=++n;
		mx[u][0]=mx[v][0]=e[i].w;
		addedge(n,u);addedge(n,v);
	}
}
inline void dfs(int x){
	seq[in[x]=++dfn]=x;
	for(int i(point[x]);i;i=edges[i].next)dfs(edges[i].v);
	out[x]=dfn;
}
inline int dy(int x){
	return lower_bound(ls+1,ls+lsnum+1,x)-ls;
}
inline hh* insert(hh* p,int l,int r,int v){
	hh* x=newnode();
	x->sz=p->sz+1;
	x->l=p->l,x->r=p->r;
	if(l==r)return x;
	int mid(l+r>>1);
	if(v<=mid)x->l=insert(p->l,l,mid,v);
	else x->r=insert(p->r,mid+1,r,v);
	return x;
}
inline int query(hh* p1,hh* p2,int l,int r,int k){
	int mid(l+r>>1);
	if(l==r)return mid;
	int tmp=p2->l->sz-p1->l->sz;
	if(k<=tmp)return query(p1->l,p2->l,l,mid,k);
	else return query(p1->r,p2->r,mid+1,r,k-tmp);
}
inline int get(int x,int v){
	for(int i(D);i>=0;i--)
		if(f[x][i]&&mx[x][i]<=v)
			x=f[x][i];
	return x;
}
int main(){
	read(n);read(m);read(Q);nn=n;
	for(int i(1);i<=n;i++){
		read(h[i]);
		ls[++lsnum]=h[i];
	}
	sort(ls+1,ls+lsnum+1);
	lsnum=unique(ls+1,ls+lsnum+1)-ls-1;
	for(int i(1),u,v,w;i<=m;i++){
		read(u);read(v);read(w);
		e[i]=(edge){u,v,0,w};
	}
	krusal();
	for(int i(1);i<=D;i++)
		for(int j(1);j<=n;j++)
			f[j][i]=f[f[j][i-1]][i-1],mx[j][i]=max(mx[j][i-1],mx[f[j][i-1]][i-1]);
	for(int i(n);i>=1;i--)if(!in[i])dfs(i);
	Null=tail;Null->l=Null->r=Null;Null->sz=0;
	rot[0]=Null;
	for(int i(1);i<=n;i++)
		if(seq[i]>nn)rot[i]=rot[i-1];
		else rot[i]=insert(rot[i-1],1,lsnum,dy(h[seq[i]]));
	int v,x,k;
	for(int i(1);i<=Q;i++){
		read(v);read(x);read(k);
		v^=ans;x^=ans;k^=ans;
		int anc=get(v,x);
		int len=rot[out[anc]]->sz-rot[in[anc]-1]->sz;
		if(len<k){puts("-1");ans=0;continue;}
		ans=ls[query(rot[in[anc]-1],rot[out[anc]],1,lsnum,len-k+1)];
		printf("%d\n",ans);
	}
	return 0;
}
```
