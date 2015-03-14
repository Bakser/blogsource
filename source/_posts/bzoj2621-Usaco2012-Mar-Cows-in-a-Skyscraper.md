title: "bzoj2621 [Usaco2012 Mar]Cows in a Skyscraper"
date: 2015-01-28 11:01:13
tags: [dp,压位]
---
题意：给你n个有权值的物品，分成好多组，每组权值和不能超过w，求最少分多少组。
<!--more-->
一个NP-Hard问题。。。。。
采用状压dp解决
一个较容易想到的是$O(n\times 3^n)$的枚举子集的算法。
换一种dp方式
$s[i]=\sum\_{j\in i} val[j]$
$f[i][j]=s[i]-g[i][j]>=w?s[i]:0$
$g[i][j]=max~f[k][j-1] ~~(k⊊i)$
 显然由f[i][j]可以得到i这个状态，分j组能否完成。
 问题是暴力计算j仍是枚举子集。。。。
 容易发现答案一定是在满足$|k|=|s|-1$的k中取到，$O(n)$枚举k比s少哪一个元素即可。
 总复杂度$O(n^2\times 2^n)$。
 代码：
 ```c++ bzoj2621.cpp
 #include <bits/stdc++.h>
const int N(262200);
int n,all,ans,w,s[N],f[N],g[N];
int main(){
	scanf("%d%d",&n,&w);all=(1<<n);
	for(int i(0);i<n;i++)scanf("%d",s+(1<<i));
	for(int i(3);i<all;i++)s[i]=s[i&(i-1)]+s[i&(-i)];
	for(int i(1);i<=n;i++){
		memcpy(g,f,sizeof f);
		for(int k(0);k<n;k++)
			for(int j(0);j<all;j++)
				if((j>>k&1)&&g[j^(1<<k)]>g[j])
					g[j]=g[j^(1<<k)];
		for(int j(0);j<all;j++)f[j]=s[j]-g[j]<=w?s[j]:0;
		if(f[all-1]==s[all-1]){ans=i;break;}
	}
	printf("%d\n",ans);
	return 0;
}
```