title: "bzoj3532:[Sdoi2014]LIS"
date: 2015-03-26 15:42:05
tags: [网络流]
---
当年真是naive+1
<!--more-->
第一问比较简单，f[i]表示i这个位置结束的LIS的长度。
把每个点拆成两个，中间连边流量为$B_{i}$
若$i\leq j,a\_{i}\leq a\_{j},f[i]+1=f[j]$则从i向j连边。
从S向每个f[i]=1的i连边，从每个f[i]=LIS的i向T连边。然后最小割。
要输出方案，所以把每个位置按c排序，从小往大贪心，判断能否选即可。
判断能否选可以在残量网络里dfs，判断是否有从i向i'的增广路，有就不在最小割里。
但是选了这个点以后要保证以后选的不和它冲突，就随便找一条增广路退流1即可。
```c++
#include<cstdio>
#include<cstring>
#include<iostream>
#include<algorithm>
#include<queue>
using namespace std;
typedef pair<int,int> pii;
#define MIN(a,b) (((a)<(b))?(a):(b))
const int N(2000);
const int M(5e6);
const int INF(0x3f3f3f3f);
struct hh{
	int u,v,cap,next;
}edges[M];
int T,n,s,t,S,tot,len,en,num,a[N],b[N],f[N],d[N],cur[N],gap[N],p[N],ans[N],point[N],e[N];
pii c[N];
bool vis[N];
inline void read(int &x){
	char c=0;int f=1;
	for(;c<'0'||c>'9';c=getchar())if(c=='-')f=-1;
	for(x=0;c>='0'&&c<='9';c=getchar())x=x*10+(c-'0');
	x*=f;
}
inline void addedge(int u,int v,int cap){
	edges[++en]=(hh){u,v,cap,point[u]};
	point[u]=en;
}
inline void Add(int u,int v,int cap){
	addedge(u,v,cap);
	addedge(v,u,0);
}
inline void bfs(){
	static queue<int> q;
	memset(d,0xff,sizeof(d));
	gap[d[t]=0]++;
	for(q.push(t);!q.empty();q.pop()){
		int x(q.front());
		for(int i(point[x]);i!=-1;i=edges[i].next)
			if(edges[i^1].cap&&d[edges[i].v]==-1){
				gap[d[edges[i].v]=d[x]+1]++;
				q.push(edges[i].v);
			}
	}
}
inline int augment(){
	int flow=INF;
	for(int x(t);x!=s;x=edges[p[x]].u)flow=MIN(flow,edges[p[x]].cap);
	for(int x(t);x!=s;x=edges[p[x]].u)edges[p[x]].cap-=flow,edges[p[x]^1].cap+=flow;
	return flow;
}
inline int isap(){
	memcpy(cur,point,sizeof(int)*(num+5));
	memset(gap,0,sizeof(gap));
	memset(p,0,sizeof(p));
	bfs();
	int res=0,x=s;
	for(;d[s]<num;){
		if(x==t)res+=augment(),x=s;
		bool flag=0;
		for(int i(cur[x]),v;i!=-1;i=edges[i].next)
			if(edges[i].cap&&d[x]-1==d[v=edges[i].v]){
				p[v]=cur[x]=i;
				x=v;
				flag=1;
				break;
			}
		if(!flag){
			int delta=num;
			for(int i(point[x]);i!=-1;i=edges[i].next)
				if(edges[i].cap&&d[edges[i].v]<delta)
					delta=d[edges[i].v],cur[x]=i;
			if(!(--gap[d[x]]))break;
			gap[d[x]=delta+1]++;
			if(x!=s)x=edges[p[x]].u;
		}
	}
	return res;
}
inline bool dfs(int x,int t){
	if(x==t)return 1;
	vis[x]=1;
	for(int i(point[x]);i!=-1;i=edges[i].next)
		if(edges[i].cap&&!vis[edges[i].v])
			if(dfs(edges[i].v,t))return 1;
	return 0;
}
inline bool dfs2(int x,int t,bool Rev,int no){
	if(x==t)return 1;
	vis[x]=1;
	for(int i(point[x]);i!=-1;i=edges[i].next){
		if(i==no||i==(no^1))continue;
		int rev=Rev?i:i^1,v=edges[i].v;
		if(edges[rev].cap&&!vis[v]){
			if(dfs2(v,t,Rev,no)){
				edges[rev].cap--,edges[rev^1].cap++;
				return 1;
			}
		}
	}
	return 0;
}
int main(){
	for(read(T);T--;){
		read(n);len=0;en=-1;tot=0;
		memset(point,-1,sizeof(point));
		memset(e,0,sizeof(e));
		memset(edges,0,sizeof(edges));
		for(int i(1);i<=n;i++)read(a[i]);
		for(int i(1);i<=n;i++)read(b[i]);
		for(int i(1);i<=n;i++)read(c[i].first),c[i].second=i;
		memset(f,0,sizeof(f));
		for(int i(1);i<=n#include<cstdio>
#include<cstring>
#include<iostream>
#include<algorithm>
#include<queue>
using namespace std;
typedef pair<int,int> pii;
#define MIN(a,b) (((a)<(b))?(a):(b))
const int N(2000);
const int M(5e6);
const int INF(0x3f3f3f3f);
struct hh{
	int u,v,cap,next;
}edges[M];
int T,n,s,t,S,tot,len,en,num,a[N],b[N],f[N],d[N],cur[N],gap[N],p[N],ans[N],point[N],e[N];
pii c[N];
bool vis[N];
inline void read(int &x){
	char c=0;int f=1;
	for(;c<'0'||c>'9';c=getchar())if(c=='-')f=-1;
	for(x=0;c>='0'&&c<='9';c=getchar())x=x*10+(c-'0');
	x*=f;
}
inline void addedge(int u,int v,int cap){
	edges[++en]=(hh){u,v,cap,point[u]};
	point[u]=en;
}
inline void Add(int u,int v,int cap){
	addedge(u,v,cap);
	addedge(v,u,0);
}
inline void bfs(){
	static queue<int> q;
	memset(d,0xff,sizeof(d));
	gap[d[t]=0]++;
	for(q.push(t);!q.empty();q.pop()){
		int x(q.front());
		for(int i(point[x]);i!=-1;i=edges[i].next)
			if(edges[i^1].cap&&d[edges[i].v]==-1){
				gap[d[edges[i].v]=d[x]+1]++;
				q.push(edges[i].v);
			}
	}
}
inline int augment(){
	int flow=INF;
	for(int x(t);x!=s;x=edges[p[x]].u)flow=MIN(flow,edges[p[x]].cap);
	for(int x(t);x!=s;x=edges[p[x]].u)edges[p[x]].cap-=flow,edges[p[x]^1].cap+=flow;
	return flow;
}
inline int isap(){
	memcpy(cur,point,sizeof(int)*(num+5));
	memset(gap,0,sizeof(gap));
	memset(p,0,sizeof(p));
	bfs();
	int res=0,x=s;
	for(;d[s]<num;){
		if(x==t)res+=augment(),x=s;
		bool flag=0;
		for(int i(cur[x]),v;i!=-1;i=edges[i].next)
			if(edges[i].cap&&d[x]-1==d[v=edges[i].v]){
				p[v]=cur[x]=i;
				x=v;
				flag=1;
				break;
			}
		if(!flag){
			int delta=num;
			for(int i(point[x]);i!=-1;i=edges[i].next)
				if(edges[i].cap&&d[edges[i].v]<delta)
					delta=d[edges[i].v],cur[x]=i;
			if(!(--gap[d[x]]))break;
			gap[d[x]=delta+1]++;
			if(x!=s)x=edges[p[x]].u;
		}
	}
	return res;
}
inline bool dfs(int x,int t){
	if(x==t)return 1;
	vis[x]=1;
	for(int i(point[x]);i!=-1;i=edges[i].next)
		if(edges[i].cap&&!vis[edges[i].v])
			if(dfs(edges[i].v,t))return 1;
	return 0;
}
inline bool dfs2(int x,int t,bool Rev,int no){
	if(x==t)return 1;
	vis[x]=1;
	for(int i(point[x]);i!=-1;i=edges[i].next){
		if(i==no||i==(no^1))continue;
		int rev=Rev?i:i^1,v=edges[i].v;
		if(edges[rev].cap&&!vis[v]){
			if(dfs2(v,t,Rev,no)){
				edges[rev].cap--,edges[rev^1].cap++;
				return 1;
			}
		}
	}
	return 0;
}
int main(){
	for(read(T);T--;){
		read(n);len=0;en=-1;tot=0;
		memset(point,-1,sizeof(point));
		memset(e,0,sizeof(e));
		memset(edges,0,sizeof(edges));
		for(int i(1);i<=n;i++)read(a[i]);
		for(int i(1);i<=n;i++)read(b[i]);
		for(int i(1);i<=n;i++)read(c[i].first),c[i].second=i;
		memset(f,0,sizeof(f));
		for(int i(1);i<=n;i++){
			f[i]=max(f[i],1);
			for(int j(i+1);j<=n;j++)
				if(a[j]>a[i])
					f[j]=max(f[j],f[i]+1);
		}
		for(int i(1);i<=n;i++)len=max(len,f[i]);
		s=1;t=(n<<1|1)+1;num=t+1;
		for(int i(1);i<=n;i++){
			if(f[i]==1)Add(s,i<<1,INF);
			if(f[i]==len)Add(i<<1|1,t,INF);
			Add(i<<1,i<<1|1,b[i]);
			e[i]=en-1;
			for(int j(i+1);j<=n;j++)
				if(a[i]<a[j]&&f[i]+1==f[j])
					Add(i<<1|1,j<<1,INF);
		}
		S=isap();
		sort(c+1,c+n+1);
		for(int i(1);i<=n;i++){
			int x(c[i].second);
			memset(vis,0,sizeof(vis));
			if(!edges[e[x]].cap&&!dfs(x<<1,x<<1|1)){
				memset(vis,0,sizeof(vis));
				ans[++tot]=x;
				dfs2(x<<1,s,1,e[x]);
				memset(vis,0,sizeof(vis));
				dfs2(x<<1|1,t,0,e[x]);
				edges[e[x]].cap=edges[e[x]^1].cap=0;
			}
		}
		printf("%d %d\n",S,tot);
		sort(ans+1,ans+tot+1);
		for(int i(1);i<=tot;i++)printf("%d ",ans[i]);
		puts("");
	}
	return 0;
};i++){
			f[i]=max(f[i],1);
			for(int j(i+1);j<=n;j++)
				if(a[j]>a[i])
					f[j]=max(f[j],f[i]+1);
		}
		for(int i(1);i<=n;i++)len=max(len,f[i]);
		s=1;t=(n<<1|1)+1;num=t+1;
		for(int i(1);i<=n;i++){
			if(f[i]==1)Add(s,i<<1,INF);
			if(f[i]==len)Add(i<<1|1,t,INF);
			Add(i<<1,i<<1|1,b[i]);
			e[i]=en-1;
			for(int j(i+1);j<=n;j++)
				if(a[i]<a[j]&&f[i]+1==f[j])
					Add(i<<1|1,j<<1,INF);
		}
		S=isap();
		sort(c+1,c+n+1);
		for(int i(1);i<=n;i++){
			int x(c[i].second);
			memset(vis,0,sizeof(vis));
			if(!edges[e[x]].cap&&!dfs(x<<1,x<<1|1)){
				memset(vis,0,sizeof(vis));
				ans[++tot]=x;
				dfs2(x<<1,s,1,e[x]);
				memset(vis,0,sizeof(vis));
				dfs2(x<<1|1,t,0,e[x]);
				edges[e[x]].cap=edges[e[x]^1].cap=0;
			}
		}
		printf("%d %d\n",S,tot);
		sort(ans+1,ans+tot+1);
		for(int i(1);i<=tot;i++)printf("%d ",ans[i]);
		puts("");
	}
	return 0;
}
```
