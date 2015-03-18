title: "bzoj2154 Crash的数字表格"
date: 2015-03-17 15:17:36
tags: [数论]
---
题意：
求$\sum\_{i=1}^{n}\sum\_{j=1}^{m}lcm(i,j)$
<!--more-->
推导：
$设n\leq m\\\ \sum\_{i=1}^{n}\sum\_{j=1}^{m}lcm(i,j)\\\=\sum\_{i=1}^{n}\sum\_{j=1}^{m}\frac{i\times j}{gcd(i,j)}\\\=\sum\_{d=1}^{n}\sum\_{i=1}^{\lfloor\frac{n}{d}\rfloor}\sum\_{j=1}^{\lfloor\frac{m}{d}\rfloor}i\times j[gcd(i,j)=1]\\\设g(n,m)=\sum\_{i=1}^{n}\sum\_{j=1}^{m}d\times i\times j[gcd(i,j)=1]\\\原式=\sum\_{d=1}^{n}d\times g(\lfloor\frac{n}{d}\rfloor,\lfloor\frac{m}{d}\rfloor)\\\不考虑计算g的话可以在O(\sqrt{n}+\sqrt{m})时间内计算\\\g(n,m)=\sum\_{i=1}^{n}\sum\_{j=1}^{m}i\times j[gcd(i,j)=1]\\\=\sum\_{i=1}^{n}\sum\_{j=1}^{m}i\times j\times \sum\_{d|i,d|j}\mu(d)\\\=\sum\_{d=1}^{n}d^{2}\times\mu(d)\times\sum\_{i=1}^{\lfloor\frac{n}{d}\rfloor}\sum\_{j=1}^{\lfloor \frac{m}{d}\rfloor}i\times j\\\=\sum\_{d=1}^{n}d^{2}\mu(d)\frac{(1+n)n}{2}\frac{(1+m)m}{2}$
如果有多组询问的话可以有另一种反演方式，做到$O(n)$线性筛预处理,$O(\sqrt(n)+\sqrt(m))$回答每次询问。
Code：
```c++
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <iostream>
using namespace std;
typedef long long LL;
const int MOD(20101009);
const int N(1e7+20);
LL n,m,tot;
LL ans(0),mu[N],prime[N];
bool vis[N];
inline LL check(LL x){
	if(x>=MOD)x%=MOD;
	if(x<0)x=(x%MOD+MOD)%MOD;
	return x;
}
inline LL calc(LL a,LL b){
	return check(a*b/2);
}
inline void sieve(int n){
	mu[1]=1;
	for(LL i(2);i<=n;i++){
		if(!vis[i])prime[++tot]=i,mu[i]=-1;
		for(int j(1);j<=tot&&i*prime[j]<=n;j++){
			vis[i*prime[j]]=1;
			if(i%prime[j]==0){mu[i*prime[j]]=0;break;}
			mu[i*prime[j]]=-mu[i];
		}
	}
	for(LL i(1);i<=n;i++)mu[i]=check(mu[i-1]+check(mu[i]*check(i*i)))%MOD;
}
inline LL S(LL n,LL m){
	return check(calc(n,n+1)*calc(m,m+1));
}
inline LL g(LL n,LL m){
	LL res(0);
	for(LL i(1),nxt(0);i<=n;i=nxt+1){
		LL d1=n/i,d2=m/i;
		nxt=min(n/d1,m/d2);
		res=check(res+check((mu[nxt]-mu[i-1])%MOD*S(d1,d2)));
	}
	return res;
}
int main(){
	scanf("%lld%lld",&n,&m);
	sieve(m);
	if(n>m)swap(n,m);
	for(LL i(1),nxt;i<=n;i=nxt+1){
		LL d1=(n/i),d2(m/i);
		nxt=min(n/d1,m/d2);
		ans=check(ans+check(calc(i+nxt,nxt-i+1)*g(d1,d2)));
	}
	printf("%lld\n",ans);	
	return 0;
}
```
