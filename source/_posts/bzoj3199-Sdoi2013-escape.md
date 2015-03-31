title: "bzoj3199:[Sdoi2013]escape"
date: 2015-03-27 07:36:34
tags: [半平面交]
---
用半平面交暴力建出voronoi图，然后相邻区域连边跑最短路即可。
<!--more-->
有组n=0的数据呵呵。
```c++
#include <cstdio>
#include <cstring>
#include <iostream>
#include <algorithm>
#include <cmath>
#include <queue>
using namespace std;
typedef double LD;
const int N(602);
const int INF(0x3f3f3f3f);
const LD eps(1e-13);
inline int read(){
	char c=0;int x,f=1;
	for(;c<'0'||c>'9';c=getchar())if(c=='-')f=-1;
	for(x=0;c>='0'&&c<='9';c=getchar())x=x*10+(c-'0');
	return x*f;
}
inline int dcmp(LD x){
	return x>eps?1:(x<-eps?-1:0);
}
struct Point{
	LD x,y;
	Point(LD _x=0,LD _y=0):x(_x),y(_y){}
	inline Point operator -(const Point &b)const{return Point(x-b.x,y-b.y);}
	inline Point operator +(const Point &b)const{return Point(x+b.x,y+b.y);}
	inline Point operator *(const LD &b)const{return Point(x*b,y*b);}
	inline void get(){x=read();y=read();}
}sta,r[N],p[N];
inline LD Ang(const Point &x){
	LD t(atan2(x.y,x.x));
	if(t<0)t+=acos(0.0)*4;
	return t;
}
inline LD cross(const Point &a,const Point &b){
	return a.x*b.y-b.x*a.y;
}
struct line{
	Point u,v;
	LD ang;
	int from;
	line(){};
	line(const Point &a,const Point &b,const int &f){
		u=a;v=b;
		ang=Ang(v);
		from=f;
	}
	inline bool operator<(const line &b)const{return dcmp(ang-b.ang)<0||(!dcmp(ang-b.ang)&&dcmp(cross(v,b.u-v))<0);}
}lines[N],deq[N];
struct hh{
	int v,next;
}edges[N*N*2];
int T,n,s,t,lnum,en,head,tail,point[N],d[N];
LD X1,Y1;#include <cstdio>
#include <cstring>
#include <iostream>
#include <algorithm>
#include <cmath>
#include <queue>
using namespace std;
typedef double LD;
const int N(602);
const int INF(0x3f3f3f3f);
const LD eps(1e-13);
inline int read(){
	char c=0;int x,f=1;
	for(;c<'0'||c>'9';c=getchar())if(c=='-')f=-1;
	for(x=0;c>='0'&&c<='9';c=getchar())x=x*10+(c-'0');
	return x*f;
}
inline int dcmp(LD x){
	return x>eps?1:(x<-eps?-1:0);
}
struct Point{
	LD x,y;
	Point(LD _x=0,LD _y=0):x(_x),y(_y){}
	inline Point operator -(const Point &b)const{return Point(x-b.x,y-b.y);}
	inline Point operator +(const Point &b)const{return Point(x+b.x,y+b.y);}
	inline Point operator *(const LD &b)const{return Point(x*b,y*b);}
	inline void get(){x=read();y=read();}
}sta,r[N],p[N];
inline LD Ang(const Point &x){
	LD t(atan2(x.y,x.x));
	if(t<0)t+=acos(0.0)*4;
	return t;
}
inline LD cross(const Point &a,const Point &b){
	return a.x*b.y-b.x*a.y;
}
struct line{
	Point u,v;
	LD ang;
	int from;
	line(){};
	line(const Point &a,const Point &b,const int &f){
		u=a;v=b;
		ang=Ang(v);
		from=f;
	}
	inline bool operator<(const line &b)const{return dcmp(ang-b.ang)<0||(!dcmp(ang-b.ang)&&dcmp(cross(v,b.u-v))<0);}
}lines[N],deq[N];
struct hh{
	int v,next;
}edges[N*N*2];
int T,n,s,t,lnum,en,head,tail,point[N],d[N];
LD X1,Y1;
bool in[N];
inline bool onleft(const line &l,const Point &p){
	return dcmp(cross(l.v,p-l.u))>0;
}
inline Point Inter(const line &a,const line &b){
	Point u=a.u-b.u;
	LD t(cross(a.v,u)/cross(a.v,b.v));
	return b.u+b.v*t;
}
inline void addedge(int u,int v){
	edges[++en]=(hh){v,point[u]};point[u]=en;
}
inline void pre(int x){
	lnum=0;
	lines[lnum++]=line(Point(0,0),Point(INF,0),t);
	lines[lnum++]=line(Point(X1,0),Point(0,INF),t);
	lines[lnum++]=line(Point(X1,Y1),Point(-INF,0),t);
	lines[lnum++]=line(Point(0,Y1),Point(0,-INF),t);
	for(int i(1);i<=n;i++)
		if(i!=x){
			Point u((r[i]+r[x])*0.5),v(r[x].y-r[i].y,r[i].x-r[x].x);
			if(!onleft(line(u,v,0),r[x]))v.x*=-1,v.y*=-1;
			lines[lnum++]=line(u,v,i);
		}
			
}
inline bool Half_planar_intersection(){
	sort(lines,lines+lnum);
	deq[head=tail=0]=lines[0];
	for(int i(1);i<lnum;i++){
		while(head+1<tail&&!onleft(lines[i],p[tail-1]))tail--;
		while(head+1<tail&&!onleft(lines[i],p[head]))head++;
		if(dcmp(lines[i].ang-deq[tail].ang)==0){if(!onleft(lines[i],deq[tail].u))deq[tail]=lines[i];}
		else deq[++tail]=lines[i];
		if(head<tail)p[tail-1]=Inter(deq[tail-1],deq[tail]);
	}
	while(head+1<tail&&!onleft(deq[head],p[tail-1]))tail--;
	if(tail-head<=1)return 0;
	p[tail]=Inter(deq[tail],deq[head]);
	return 1;
}
inline bool inside(){
	for(int i(head);i<=tail;i++)
		if(!onleft(deq[i],sta))
			return 0;
	return 1;
}
inline int bfs(){
	static queue<int> q;
	memset(d,0x3f,sizeof(d));
	memset(in,0,sizeof(in));
	d[s]=0;in[s]=1;
	for(q.push(s);!q.empty();q.pop()){
		int x(q.front());in[x]=0;
		for(int i(point[x]);i;i=edges[i].next)
			if(d[x]+1<d[edges[i].v]){
				d[edges[i].v]=d[x]+1;
				if(!in[edges[i].v])q.push(edges[i].v);
			}
	}
	return d[t];
}
int main(){
	for(T=read();T--;){
		n=read();
		X1=read();Y1=read();sta.get();
		en=0;t=n+1;
		memset(point,0,sizeof(point));
		for(int i(1);i<=n;i++)r[i].get();
		if(!n){
			puts("0");
			continue;
		}
		for(int i(1);i<=n;i++){
			pre(i);
			Half_planar_intersection();
			for(int j(head);j<=tail;j++)
				addedge(i,deq[j].from);
			if(inside())s=i;
		}
		printf("%d\n",bfs());
	}
	return 0;
}
bool in[N];
inline bool onleft(const line &l,const Point &p){
	return dcmp(cross(l.v,p-l.u))>0;
}
inline Point Inter(const line &a,const line &b){
	Point u=a.u-b.u;
	LD t(cross(a.v,u)/cross(a.v,b.v));
	return b.u+b.v*t;
}
inline void addedge(int u,int v){
	edges[++en]=(hh){v,point[u]};point[u]=en;
}
inline void pre(int x){
	lnum=0;
	lines[lnum++]=line(Point(0,0),Point(INF,0),t);
	lines[lnum++]=line(Point(X1,0),Point(0,INF),t);
	lines[lnum++]=line(Point(X1,Y1),Point(-INF,0),t);
	lines[lnum++]=line(Point(0,Y1),Point(0,-INF),t);
	for(int i(1);i<=n;i++)
		if(i!=x){
			Point u((r[i]+r[x])*0.5),v(r[x].y-r[i].y,r[i].x-r[x].x);
			if(!onleft(line(u,v,0),r[x]))v.x*=-1,v.y*=-1;
			lines[lnum++]=line(u,v,i);
		}
			
}
inline bool Half_planar_intersection(){
	sort(lines,lines+lnum);
	deq[head=tail=0]=lines[0];
	for(int i(1);i<lnum;i++){
		while(head+1<tail&&!onleft(lines[i],p[tail-1]))tail--;
		while(head+1<tail&&!onleft(lines[i],p[head]))head++;
		if(dcmp(lines[i].ang-deq[tail].ang)==0){if(!onleft(lines[i],deq[tail].u))deq[tail]=lines[i];}
		else deq[++tail]=lines[i];
		if(head<tail)p[tail-1]=Inter(deq[tail-1],deq[tail]);
	}
	while(head+1<tail&&!onleft(deq[head],p[tail-1]))tail--;
	if(tail-head<=1)return 0;
	p[tail]=Inter(deq[tail],deq[head]);
	return 1;
}
inline bool inside(){
	for(int i(head);i<=tail;i++)
		if(!onleft(deq[i],sta))
			return 0;
	return 1;
}
inline int bfs(){
	static queue<int> q;
	memset(d,0x3f,sizeof(d));
	memset(in,0,sizeof(in));
	d[s]=0;in[s]=1;
	for(q.push(s);!q.empty();q.pop()){
		int x(q.front());in[x]=0;
		for(int i(point[x]);i;i=edges[i].next)
			if(d[x]+1<d[edges[i].v]){
				d[edges[i].v]=d[x]+1;
				if(!in[edges[i].v])q.push(edges[i].v);
			}
	}
	return d[t];
}
int main(){
	for(T=read();T--;){
		n=read();
		X1=read();Y1=read();sta.get();
		en=0;t=n+1;
		memset(point,0,sizeof(point));
		for(int i(1);i<=n;i++)r[i].get();
		if(!n){
			puts("0");
			continue;
		}
		for(int i(1);i<=n;i++){
			pre(i);
			Half_planar_intersection();
			for(int j(head);j<=tail;j++)
				addedge(i,deq[j].from);
			if(inside())s=i;
		}
		printf("%d\n",bfs());
	}
	return 0;
}
```
