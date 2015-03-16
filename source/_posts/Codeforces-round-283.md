title: "Codeforces round #283"
date: 2014-12-19 21:18:33
tags: [Codeforces]
---
好像是搬家后第一次写cf题解~
<!--more-->
<del>感觉最近好颓啊啊啊啊啊。
沉浸在文化课中不能自拔。。。
一个礼拜也刷不了几道题。。。。。
在这么下去就滚粗无疑了。。。。。。。</del>
算是vp吧。
感觉这场ABC难度都一般，DE惯例不会。。。。我就是弱。
老规矩题解选中现形。

A：给你一个n×m的字符矩阵，每次可以删去一列。问最少删多少列使得该矩阵的行之间是按字典序排列的。
题解：<Font color = White>贪心从前往后枚举每一列，若这一列不和谐就肯定要干掉，注意前面已经满足字典序关系的相邻两行这里就不用判不和谐了。</Font color = White>
代码君：
```c++ A
#include<cstdio>
int n,m,ans;
bool used[200];
char a[200][200];
int main(){
	scanf("%d%d",&n,&m);
	for(int i(1);i<=n;i++)
		scanf("%s",a[i]);
	for(int i(0);i<m;i++){
		bool f=0;
		for(int j(1);j<n&&!f;j++)
			f|=(a[j][i]>a[j+1][i]&&!used[j]);
		ans+=f;
		if(!f)for(int j(1);j<n;j++)used[j]|=(a[j][i]<a[j+1][i]);
	}
	printf("%d\n",ans);
	return 0;
}
```
B：有俩逗比经常打网球，他们的赛制是分场打，一场有好多回合，当一个人赢了t个回合他就赢了这场。当一个人赢了s场就赢了这场比赛。
  给你一个长度为n的胜负序列（即每回合是谁赢），输出所有不同方案的s和t使得这个序列是合法的。
题解：<Font color = White>容易发现对于一个确定的t，s是唯一的。我们枚举每个t，问题转化为怎样判定。对每个人的胜负做一个前缀和，设为s数组。然后再把前缀和里每个值的位置记录下来，设为p数组。设当前判断到的位置是x，则下一个位置就是min(p1[s1[x]+t],p2[s2[x]+t]),这样不断跳看是否能恰好覆盖整个区间，且最后赢的一个也是全局赢的场数最多的就好了。容易发现每次至少跳t长度。总复杂度n+n/2+n/3为调和级数，是O(nlogn)的。</Font color = White>
代码君：
```c++ B
#include<bits/stdc++.h>
using namespace std;
typedef pair<int,int> pii;
const int N(1e5+200);
int n,a[N],s1[N],s2[N],p1[N*3],p2[N*3];
vector<pii> ans;
int main(){
	scanf("%d",&n);
	for(int i(1);i<=n;i++)scanf("%d",a+i);
	for(int i(1);i<=n;i++){
		s1[i]=s1[i-1]+(a[i]==1);
		s2[i]=s2[i-1]+(a[i]==2);
	}
	for(int i(n);i>=1;i--)
		p1[s1[i]]=i,p2[s2[i]]=i;
	for(int i(1);i<=n;i++){
		int x=0,a1(0),a2(0),last=0;
		while(x!=n){
			int px=p1[s1[x]+i],py=p2[s2[x]+i];
			if(!px&&!py){x=-1;break;}
			x=n+200;
			if(px)x=px;
			if(py)x=min(x,py);
			if(x==px)a1++,last=1;
			if(x==py)a2++,last=2;
			if(!x)break;
		}
		if(a1==a2||x!=n)continue;
		int winner=1;
		if(a2>a1)winner=2;
		if(last!=winner)continue;
		ans.push_back(pii(max(a1,a2),i));
	}
	sort(ans.begin(),ans.end());
	printf("%d\n",ans.size());
	for(int i(0);i<ans.size();i++)
		printf("%d %d\n",ans[i].first,ans[i].second);
	return 0;
}
```
C：有一场演唱会，有n个部分，每个部分需要的音域从ai到bi，有m个歌手，能演唱的音域从ci到di,能演出ki次，问能否完成演唱会。能的话输出“YES”并输出每一部分由谁演唱，否则输出“NO”
题解：<Font color = White>贪心，把歌手和部分一块按左端点排序，从左往右扫每次遇到一个歌手就把他的右端点丢到set里，遇到一个部分就找set里右端点比它右端点大的第一个作为答案。用够了k次就删。</Font color = White>
代码君：
```c++ C
#include<bits/stdc++.h>
using namespace std;
const int N(1e5+20);
struct pii{
	int x,y;
	pii(int _x=0,int _y=0):x(_x),y(_y){}
	bool operator<(const pii &b)const{
		if(x!=b.x)return x<b.x;
		return y<b.y;
	}
};
struct hh{
	int l,r,p;
	bool f;
	bool operator<(const hh &b)const{
		if(l!=b.l)return l<b.l;
		return f<b.f;
	}
}a[N<<1];
int n,m,c[N],ans[N];
set<pii> s;
int main(){
	scanf("%d",&n);
	for(int i(1);i<=n;i++)
		scanf("%d%d",&a[i].l,&a[i].r),a[i].p=i,a[i].f=1;
	scanf("%d",&m);
	for(int i(1);i<=m;i++)
		scanf("%d%d%d",&a[n+i].l,&a[n+i].r,c+i),a[n+i].p=i,a[n+i].f=0;
	sort(a+1,a+n+m+1);
	for(int i(1);i<=n+m;i++)
		if(a[i].f){
			set<pii>::iterator j=s.lower_bound(pii(a[i].r,a[i].p));
			if(j==s.end()){
				puts("NO");
				return 0;
			}
			ans[a[i].p]=j->y-n;
			if(!(--c[j->y-n]))
				s.erase(j);
		}
		else s.insert(pii(a[i].r,n+a[i].p));
	puts("YES");
	for(int i(1);i<=n;i++)
		printf("%d ",ans[i]);
	puts("");
	return 0;
}

```