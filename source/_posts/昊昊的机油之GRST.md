layout: 
title: bzoj3704昊昊的机油之GRST
date: 2014-11-27 15:33:30
tags: [Greedy,Constructive]
---
比较神的题。
<!--more-->
思想就是首先随便构造一个解然后贪心地逼近最优解。
考虑假如我们每次只能对一个后缀区间操作，则可以得到一组解。
考虑到若构造出的这组解前后两个若差的有点大，则没必要从前一个继承操作，可以自己来一次新的。
所以对操作数序列差分然后根据两个相邻的差的数贪心就好了。
就是先干掉相差为3的，然后2,1.
代码：
```c++ bzoj3704
#include<cstdio>
using namespace std;
#define min(a,b) (((a)<(b))?(a):(b))
const int N(1e7+20);
int n,a[N],b[N],c[N],d[N],f[N],ans;
inline void calc(int h){
    int num(0);
    for(int i(1);i<=n;i++)
        if(d[i]==h&&f[i]>num)
            ans-=h,d[i]-=4,num++;
    for(int i(1);i<=n;i++)
        c[i]=c[i-1]+d[i];
    for(int i(n);i>=1;i--)
        f[i]=min(c[i]/4,f[i+1]);
}
inline void read(int &x){
	char c=0;int f=1;
	for(;c<'0'||c>'9';c=getchar())if(c=='-')f=-1;
	for(x=0;c>='0'&&c<='9';c=getchar())(x*=10)+=(c-'0');
	x*-f;
}
int main(){
    read(n);
    for(int i(1);i<=n;i++)
        read(a[i]);
    for(int i(1);i<=n;i++)
        read(b[i]);
    for(int i(1);i<=n;i++){
        c[i]=b[i]-a[i];
        if(c[i]<c[i-1])c[i]+=(c[i-1]-c[i])/4*4;
        while(c[i]<c[i-1])c[i]+=4;
        f[i]=c[i]/4;
        d[i]=c[i]-c[i-1];
    }
    ans=c[n];f[n+1]=0x7fffffff;
    for(int i(3);i>=1;i--)
        calc(i);
    printf("%d\n",ans);
    return 0;
}
```