title: kd-tree
date: 2014-10-30 21:48:35
tags: Data_structure
---
蛋疼了好久这个Blog终于能用了。
开个新坑
<!--more-->
#kd-tree
kd tree是一种非常神的数据结构。。。。实际上也许不能算完全意义上的数据结构。因为它只是以特别的方式把k维点维护了起来，然后方便爆搜的时候剪枝。本身并不会维护答案。

那么是什么方式呢？

中位数！！！！

##kd-tree的建立
一般的kd tree建树是这样的
按第一维排序，取中位数作为根节点，然后递归建立左右子树，但左右子树就要按下一维排序了。
当然还有取方差大的维度排序的之类神做法，反正我不会就是了~
这样做就体现出了kdtree的意义所在，这一维度比某点大的一定在右子树，小的一定在左子树，这就可以剪枝了对不对？
code：

```c++
inline void build(node* &x,int l,int r,int _d){
	newnode(x);
	now=_d;int mid((l+r)>>1);
	nth_element(p+l,p+mid,p+r+1);
	(*x)=p[mid];
	if(l<mid)build(x->s[0],l,mid-1,(_d+1)%D);
	if(mid<r)build(x->s[1],mid+1,r,(_d+1)%D);
	x->upd();
}
```
##kd-tree的插入
其实这是一个不是很常用的操作，具体实现类似BST的插入。
注意插入过多会导致不平衡，据说解决方法是过一段时间暴力重建kd tree
反正我还没见过这么坑的题。

```c++
inline void insert(node* &x,int _d){
    int f(P.d[_d]>=x->d[_d]);
    if(x->s[f])insert(x->s[f],(_d+1)%D);
    else{
        newnode(x->s[f]);
        *(x->s[f])=P;
    }
    x->upd();
}
```
##kd-tree的查询
这是kd tree最精华的一部分，同时也是我不会给出代码的一部分，原因下文会说。
大体思想就是，根据查询点与当前点这一维度的大小关系决定先进入哪一个子树。
再写一个*估价函数*决定是否进入另一个子树。
这个估价函数是最重要的，它可以利用这个节点维护的信息（一般是子树每一维度的min，max）来剪枝。
这个估价函数因人而异，所以我不会给出代码。

