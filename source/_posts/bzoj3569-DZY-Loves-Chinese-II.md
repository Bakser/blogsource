title: "bzoj3569:DZY Loves Chinese II"
date: 2015-03-31 10:38:34
tags: [动态图]
---
WC2015时xyz讲过。。。
<!--more-->
一种很神非确定性随机做法。。。
随便dfs出一棵生成树，非树边权值随机，树边权值为覆盖它的非树边权值异或和。只有当一条树边和所有覆盖它的非树边都被割掉才会不联通。
然后这样的性质跟每个点周围所有边权异或和为0是等价的。证明可以用归纳法，生成树上的叶子只有一条树边，跟它相连的非树边都是覆盖这条树边的，它们异或和为0。然后把这个叶子缩掉继续归纳即可。
所以最后求每条边的权值也可以这样做，一遍dfs，先处理子树，然后这条树边的权值就确定了。
错误概率的证明没看懂>_<
Code:
```
#include <bits/stdc++.h>
using namespace std;
const int N(1e5+200);
const int M(5e5+200);
typedef unsigned long long LL;
struct hh{
    int v,next;
}edges[M*2];
int n,m,k,Q,en=1,fa[N],point[N],cnt(0);
int w[M],val[N],lb[70];
bool tree[M],vis[N];
inline void read(int &x){
    char c=0;
    for(;c<'0'||c>'9';c=getchar());
    for(x=0;c>='0'&&c<='9';c=getchar())x=x*10+(c-'0');
}
inline void addedge(int u,int v){
    edges[++en]=(hh){v,point[u]};
    point[u]=en;
}
inline int find(int x){
    return fa[x]==x?x:fa[x]=find(fa[x]);
}
inline LL get(){
    return rand()<<16|rand();
}
inline void build_tree(int x,int fa){
    vis[x]=1;
    for(int i(point[x]);i;i=edges[i].next){
        int v(edges[i].v);
        if(!vis[v])
            build_tree(v,x),tree[i>>1]=1;
        else if(v!=fa&&w[i>>1]==-1){
            w[i>>1]=rand();
            val[x]^=w[i>>1];
            val[v]^=w[i>>1];
        }
    }
}
inline void dfs(int x,int fa){
    for(int i(point[x]);i;i=edges[i].next)
        if(tree[i>>1]&&edges[i].v!=fa){   
            int v(edges[i].v);
            dfs(v,x);
            w[i>>1]=val[v];
            val[x]^=w[i>>1];
        }
}
inline bool add_base(LL x){
    for(int i(63);i>=0;i--)
        if(x>>i&1){
            if(!lb[i]){lb[i]=x;break;}
            else x^=lb[i];
        }
    return x!=0;
}
int main(){
    srand(10086);
    read(n);read(m);
    memset(w,-1,sizeof(w));
    for(int i(1);i<=n;i++)fa[i]=i;
    for(int i(1),u,v;i<=m;i++){
        read(u);read(v);
        addedge(u,v);
        addedge(v,u);
    }
    build_tree(1,0);
    dfs(1,0);
    for(read(Q);Q--;){
        read(k);
        bool ans=1;
        memset(lb,0,sizeof(lb));
        for(int i(1),x;i<=k;i++){
            read(x);
            x^=cnt;
            if(!add_base(w[x]))
                ans=0;
        }
        cnt+=ans;
        puts(ans?"Connected":"Disconnected");
    }
    return 0;
}
```
