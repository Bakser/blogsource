title: "bzoj3806[Neerc2011]Dictionary Size"
date: 2015-01-02 11:15:58
tags: [Data_structure]
---
很容易想到先处理出所有不重复的前后缀，然后乘起来。
但是会有重复。。。。
重复情况可能像这样：
aaaabb+bbaa=aaaabbbbaa
aaaa+bbbbaa=aaaabbbbaa
注意到引起重复一定是因为某个前缀的一段后缀与某个后缀的一段前缀相同。
这种相同可以特殊化到一个字母上，容易发现某个前缀跟某个后缀有一个字母相同则一定产生重复，因为这个前缀的所有前缀都在前缀集合里，对于后缀也是。
然后做法就很显然了，正反建两棵trie，统计到每个字母结尾的前/后缀数，然后总数减去相同字母的前后缀数乘起来就好了。
比较坑的一点是若一个单词只有一个字母就会造成多减，因为它实际上不会造成重复。
所以最后答案要加上不同的单字母字符串数。
然后我被卡空间了T_T
一开始用数组写，后来改小数组，后来改指针，后来内存回收才过。。。。
再丧病是不是只有动态开儿子才能过QAQ
```c++ bzoj3806
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <queue>
using namespace std;
const int N(1e4+1);
typedef long long LL;
int n,num[2][26],cnt[2];
bool vis[26];
char s[N][42];
struct hh{hh *son[26];}*root[2];
queue<hh*> pool;
inline hh* newnode(){
    hh* res;
    if(pool.size())res=pool.front(),pool.pop();
    else res=new hh;
    memset(res->son,0,sizeof(res->son));
    return res;
}
inline void insert(char *s,int h){
    int len(strlen(s));
    hh* now(root[h]);
    for(int i(0);i<len;i++){
        int f(s[i]-'a');
        if(!now->son[f]){
            cnt[h]++;
           if(i)num[h][f]++;
            now->son[f]=newnode();
        }
        now=now->son[f];
    }
}
inline void dfs(hh* x){
    pool.push(x);
    for(int i(0);i<26;i++)
        if(x->son[i])
            dfs(x->son[i]);
}
int main(){
    scanf("%d",&n);
    LL ans(0);
    for(int i(1);i<=n;i++){
        scanf("%s",s[i]);
        if(strlen(s[i])==1&&!vis[s[i][0]-'a'])ans++,vis[s[i][0]-'a']=1;
    }
    root[0]=newnode();for(int i(1);i<=n;i++)insert(s[i],0);
    dfs(root[0]);
    for(int i(1);i<=n;i++)reverse(s[i],s[i]+strlen(s[i]));
    root[1]=newnode();for(int i(1);i<=n;i++)insert(s[i],1);
    ans+=LL(cnt[0])*LL(cnt[1]);
    for(int i(0);i<26;i++)ans-=LL(num[0][i])*LL(num[1][i]);
    printf("%lld\n",ans);
    return 0;
}
```