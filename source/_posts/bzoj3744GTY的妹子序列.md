title: bzoj3744GTY的妹子序列
date: 2014-11-16 15:29:30
tags: 出的逗比题
---
某天lzt大爷问了我一道逗比题。
然后我很快口胡了一个解法。
恩然后就是这道题了。
分块显然。
（全文假设块的大小为 $\sqrt(n)$ ）
 $ O(n \sqrt(n) log(n)) $ 预处理出第i块到第j块的逆序对数。
然后对于每个询问，整块的可以$ O(1) $获得，考虑处理两边零散的部分。
对于一个数 $ a_{i} $ 我们需要获得就是比它大或小的数的个数。
对于整块的部分我们可以用前缀和的思想 $ O(n\sqrt(n)) $ 预处理出从1到每个块起点大于/小于某个数的数的个数，则可以差分回答每次询问。(有的神犇这里用的主席树，然后就喷卡常数。。。。。。。。。不做评论)
而零散的部分直接上树状数组就好了，记得清空。
```c++ bzoj3744
#include<bits/stdc++.h>
using namespace std;
const int N(5e4+201);
const int S(231);
typedef unsigned int ui;
inline void read(int &x){
	char c=0;int f=1;
	for(;c<'0'||c>'9';c=getchar())if(c=='-')f=-1;
	for(x=0;c>='0'&&c<='9';c=getchar())(x*=10)+=(c-'0');
	x*=f;
}
int n,m,a[N],ls[N],lsnum,sz,bn;
ui c[N],f[S][S],s[2][N],pre[S][N],succ[S][N],ans,now,*ss;
inline int find(int x){
	int l(1),r(lsnum-1);
	while(l<r){
		int mid(l+r>>1);
		(ls[mid]<x)?l=mid+1:r=mid;
	}
	return r;
}
inline int bel(int pos){
	return pos/sz+bool(pos%sz);
}
inline int query(int p){
	int res(0);
	for(int i(p);i;i-=i&(-i))res+=c[i];
	return res;
}
inline void update(int p,int d){
	for(int i(p);i<=lsnum;i+=i&(-i))c[i]+=d;
}
inline int calc(int sta,int ed){
	int x(0);
	for(int k(sta);k<=min(ed,n);k++){
		x+=query(lsnum)-query(a[k]);
		update(a[k],1);
	}
	return x;
}
inline int ask(int l,int r){
	int res(0),b1(bel(l)),b2(bel(r));
	if(b1+1>b2-1){
		res=calc(l,r);
		for(int i(l);i<=r;i++)
			update(a[i],-1);
		return res;
	}
	res=f[b1+1][b2-1];
	for(int k(b1*sz);k>=l;k--){
		res+=query(a[k]-1)+pre[b2-1][a[k]-1]-pre[b1][a[k]-1];
		update(a[k],1);
	}
	for(int k((b2-1)*sz+1);k<=r;k++){
		res+=query(lsnum)-query(a[k])+succ[b2-1][a[k]+1]-succ[b1][a[k]+1];
		update(a[k],1);
	}
	for(int k(b1*sz);k>=l;k--)update(a[k],-1);
	for(int k((b2-1)*sz+1);k<=r;k++)update(a[k],-1);
	return res;
}
int main()
{
	read(n);
	sz=(int)sqrt((double)n);bn=bel(n);
	for(int i(1);i<=n;i++)read(a[i]),ls[++lsnum]=a[i];
	sort(ls+1,ls+lsnum+1);
	lsnum=unique(ls+1,ls+lsnum+1)-ls;
	for(int i(1);i<=n;i++)a[i]=find(a[i]);
	ss=s[now];
	for(int j(1);j<=bn;j++){
		int sta((j-1)*sz+1),ed(j*sz);
		for(int i(1);i<lsnum;i++)*(ss+i)=s[now^1][i];
		for(int i(sta);i<=ed;i++)(*(ss+a[i]))++;
		for(int i(1);i<lsnum;i++)pre[j][i]=*(ss+i)+pre[j][i-1];
		for(int i(lsnum-1);i>=1;i--)succ[j][i]=*(ss+i)+succ[j][i+1];
		ss=s[now^=1];
	}
	for(int i(1);i<=bn;i++){
		for(int j(i);j<=bn;j++)f[i][j]=f[i][j-1]+calc((j-1)*sz+1,j*sz);
		memset(c,0,sizeof(c));
	}
	for(read(m);m--;){
		int l,r;
		read(l);read(r);
		l^=ans;r^=ans;
		printf("%d\n",ans=ask(l,r));
	}
	return 0;
}
```