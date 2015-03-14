title: "bzoj1109[POI2007]堆积木klo"
date: 2014-11-02 08:27:21
tags: bzoj
---
###[题目描述]
Mary在她的生日礼物中有一些积木。那些积木都是相同大小的立方体。每个积木上面都有一个数。Mary用他的所有积木垒了一个高塔。妈妈告诉Mary游戏的目的是建一个塔，使得最多的积木在正确的位置。一个上面写有数i的积木的正确位置是这个塔从下往上数第i个位置。Mary决定从现有的高塔中移走一些，使得有最多的积木在正确的位置。请你告诉Mary她应该移走哪些积木。
输入文件的第一行为一个数n，表示高塔的初始高度。第二行包含n个数a1,a2,...,an，表示从下到上每个积木上面的数。(1<=n<=100000,1<=ai<=1000000)。
###[题解]
容易看出最终状态中i能在j的上面的条件是i>j，a[i]>a[j]且$ a[i]-a[j]<=i-j $
一个显然的做法是按a[i]-[i]做最长不降子序列。
这是错的，因为不能保证$ i>j $
我们对不等式变形
$ a[i]-a[j]<=i-j $
$ a[i]-i<=a[j]-j $
式一：$ i-a[i]>=j-a[j] $
式二：$ a[i]>a[j] $
根据可加性，得式三： $ i>=j $
所以满足式一和式二就不用考虑式三。
按a[i]-i排序，按a[i]做LIS就好了。
Code：
```c++ bzoj1109.cpp
#include<bits/stdc++.h>
using namespace std;
typedef pair<int,int> pii;
const int N(1e5+200);
int n,len,t[N];
vector<pii> a;
int main(){
	scanf("%d",&n);
	for(int i(1),x;i<=n;i++){
		scanf("%d",&x);
		if(x<=i)a.push_back(pii(i-x,x));
	}
	sort(a.begin(),a.end());
	for(int i(0);i<a.size();i++){
		int pos=lower_bound(t,t+len,a[i].second)-t;
		t[pos]=a[i].second;
		len=max(len,pos+1);
	}
	printf("%d\n",len);
	return 0;
}
```