title: "CH round #60滚粗记"
date: 2015-01-02 14:09:27
tags:
---
比赛页面 [在这](http://162.105.80.126/contest/CH%20Round%20%2360%20-%20Story%20Of%20the%20OI%20Class%20II%20%E5%85%83%E6%97%A6%E6%AC%A2%E4%B9%90%E8%B5%9B)
<!--more-->
由于以前打CH总是打到一半去浪，所以一直是绿名。。。。。
前两天bc黄名了就想要不在2015年前把CH也搞成蓝名之类的。。。。。。
然后滚粗了。。。。
看A题，卧槽真语文题。。。。感觉没什么意思就弃掉了。。。。
B题一看就有大概思路了，感觉很可做。
题意大约就是一个9行不一定多么长的矩阵f[i][j]=f[i][j-9]*i，前9
列都是123456789，求某个元素值或子矩阵和。
某个元素值显然就是傻逼快速幂。
子矩阵和因为总共只有9行，每行统计前缀和然后一减就出来了。
统计每行到某个端点的前缀和就整块的等比数列求和公式，零散的直接搞。
什么三进制41位？
好吧后两位是mod9的值前面的是div9的值，然后前面的对模数减1取模就是幂指数的循环节了对不对。。。
很快写完了，然后死活过不了样例！！！
调了1h+中间各种弃疗。。。。
准备愉快爆0时突然发现模数我打的是1007！！！！！！
当时想直接去死算了。。。改了接着过了pretest。
C题感觉像trie上打标记？不管了，反正rank也能看了，去吃饭。。。。



有的神犇可能已经看出滚粗点了。。。。
“前面的对模数减1取模就是幂指数的循环节了对不对”对你一脸翔！！！
行数为1时直接乘起来好吗！！！！！！
mod个翔的10006！！！！！
嘟噜噜，滚粗啦。。。。
绿的更深沉了呵呵
不好意思我语文和数学都不好。。。。。
这么SB去什么WC，滚回家养猪吧。。。。
然后发下数据和标程才发现3^39不会爆longlong。。。。标程一个longlong就存下了，根本没用费马小定理。。。
附赠丑陋的B题code：
```c++ CH#60B
#include <cstdio>
#include <iostream>
#include <cstring>
#include <algorithm>
#include <cctype>
using namespace std;
typedef long long LL;
typedef pair<LL,LL> pii;
const LL MOD(10007);
int n;
LL ext[2],r,p;
inline LL power(LL x,LL k){
    LL res(1);
    for(;k;k>>=1){
        if(k&1)(res*=x)%=MOD;
        (x*=x)%=MOD;
    }
    return res%MOD;
}
inline LL read_(){
    char c=0;LL res(0);
    for(;c<'0'||c>'9';c=getchar());
    for(;c>='0'&&c<='9';c=getchar())(res*=3)+=(c-'0');
    return res;
}
inline pii read__(int c){
   pii res(0,0);char s[50];
    scanf("%s",s);
    int len(strlen(s));
    if(len<=2){
    	for(int i(0);i<len;i++)
    		res.second=(res.second*3+s[i]-'0');
    	return res;
    }
    for(int i(0);i<len-2;i++){
    	res.first=(res.first*3+s[i]-'0')%(MOD-1);
    	if(r==1)
			ext[c]=(ext[c]*3+s[i]-'0')%MOD;
    }
    res.second=(s[len-2]-'0')*3+s[len-1]-'0';
    return res;
}
inline LL inv(LL x){
    return power(x,MOD-2);
}
inline LL jian(LL x,LL y,LL MD=MOD){
    return ((x-y)%MD+MD)%MD;
}
inline LL ask(LL x,pii p,int c){
    LL s1(0),b(45);
    if(p.first){
		if(x==1)s1=(b*ext[c])%MOD;
   		else {
    		LL t=jian(power(x,p.first),1),t2=inv(x-1);
    		t=(t*t2)%MOD;
    		s1=(b*t)%MOD;
  	  }
    }
    for(int i(1);i<=p.second;i++){
		if(x==1)(s1+=i)%=MOD;
       else (s1+=i*power(x,p.first))%=MOD;
    }
    return s1;
}
int main(){
    cin>>n;
    if(n==1){
        LL i=read_();
        LL j=read_();
        int x(j%9);
        if(!x)x=9;
        x=(x*power(i,(j-1)/9))%MOD;
        printf("%d\n",x);
    }
    else{
        r=read_();
        pii t=read__(0);
        if(t.second)t.second--;
        else t.first=jian(t.first,1),ext[0]=jian(ext[0],1,MOD-1),t.second=8;
        p=read_();
        pii q=read__(1);
        LL ans(0);
        for(int i(r);i<=p;i++){
			LL ans2=ask(i,t,0);
			LL ans1=ask(i,q,1);
            ans=(ans+jian(ans1,ans2))%MOD;
        }
        cout<<ans<<endl;
    }
    return 0;
}
```