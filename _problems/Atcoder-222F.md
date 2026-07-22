---
layout: post
title: "222F-Expensive Expense"
tags: ["Atcoder"]
---

> 20260722

[Atcoder 222F](https://atcoder.jp/contests/abc222/tasks/abc222_f)

<br>

**題敘**

在 $N$ 個頂點的樹上，對於每個節點 $i$ 找 $max(dist(i,j) + D_{j})$ 其中 $i \neq j$。

$2 \leq N \leq 2\times 10^{5}$

<br>


**思路**


&lt;M一&gt;

這是一個吃毒的作法。

先選定一個根 $r$，預處理所有的距離 $dist(r,i) + d_{i}$，其中最大值就是答案。

在 dfs 一次，當從節點 $u$ 經過權重 $w$ 的邊走到節點 $v$ 時，會造成 $u$ 子樹內的所有距離減少 $w$，其餘增加 $w$，那再查詢最大值就是答案了。

要對子樹做操作，不妨使用樹壓平變成一個序列。區間查詢極值、區間加值，使用懶惰標記線段樹即可。

Time : $O(N \log N)$ 

<br>

Code : 

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long 
#define p pair<int,int>
#define F first
#define S second

int n;
vector<vector<p>>g;
vector<int>t,sz,dis,ans,ord;

void init(){
    g.resize(n); 
    t.resize(n);
    sz.resize(n); 
    dis.resize(n);
    ans.resize(n);
}

void dfs1(int v,int f){
    t[v] = ord.size();
    ord.push_back(v);
    sz[v] = 1;
    for(auto [u,w] : g[v]){
        if(u==f) continue;
        dis[u] = dis[v] + w;
        dfs1(u,v);
        sz[v] += sz[u];
    }
}

struct Node{
    int data , tag;
};

vector<Node>seg;

int get(int id){
    return(seg[id].data + seg[id].tag);
}

void push(int id){
    seg[id*2+1].tag += seg[id].tag; 
    seg[id*2+2].tag += seg[id].tag; 
    seg[id].data = get(id);
    seg[id].tag = 0;
}

void pull(int id){
    seg[id].data = max(get(id*2+1),get(id*2+2));
    seg[id].tag = 0;
}

void Build(int l,int r,int id){
    if(l==r){
        seg[id].data = dis[ord[l]];
        seg[id].tag = 0;
        return;
    }
    int m = (l+r)/2;
    Build(l,m,id*2+1); Build(m+1,r,id*2+2);
    pull(id);
}

int query(int ql,int qr,int l,int r,int id){
    if(qr<l || r<ql) return(0);
    if(ql<=l && r<=qr) return(get(id));
    push(id);
    int m = (l+r)/2;
    return(max(query(ql,qr,l,m,id*2+1),query(ql,qr,m+1,r,id*2+2)));
}

void update(int ql,int qr,int val,int l,int r,int id){
    if(qr<l || r<ql) return;
    if(ql<=l && r<=qr){
        seg[id].tag += val;
        return;
    }
    push(id);
    int m = (l+r)/2;
    update(ql,qr,val,l,m,id*2+1);
    update(ql,qr,val,m+1,r,id*2+2);
    pull(id);
}

void dfs2(int v,int f){
    for(auto [u,w]:g[v]){
        if(u==f) continue;
        update(0,n-1,w,0,n-1,0); update(t[u],t[u]+sz[u]-1,-2*w,0,n-1,0);
        ans[u] = max(query(0,t[u]-1,0,n-1,0),query(t[u]+1,n-1,0,n-1,0));
        dfs2(u,v);
        update(0,n-1,-w,0,n-1,0); update(t[u],t[u]+sz[u]-1,2*w,0,n-1,0);
    }
}

signed main(){
    ios::sync_with_stdio(0),cin.tie(0);
    cin>>n;
    init();

    int A,B,C;
    for(int i=0;i<n-1;i++){
        cin>>A>>B>>C;
        A--; B--;
        g[A].push_back({A,C});
        g[B].push_back({B,C});
    }

    vector<int>D(n);
    for(int i=0;i<n;i++) cin>>D[i];

    dfs1(0,0);

    for(int i=0;i<n;i++) dis[i] += D[i];

    seg.resize(4*n);
    Build(0,n-1,0);

    ans[0] = query(1,n-1,0,n-1,0);
    dfs2(0,0);

    for(int i:ans) cout<<i<<'\n';
    return(0);
}
```

<br>

&lt;M二&gt;

一樣預處理所有距離。

一個節點 $i$ 的 $max(dis(i,j) + D[j])$，其中 $j$ 跟 $i$ 可以分成以下幾種 : 

case 1: $i$ 的子孫節點

case 2: 非 $i$ 的子孫節點

- $i$ 的祖先節點

- $i$ 的兄弟節點



令 $up[i]$ , $down[i]$ 表 $max(dis(i,j) + D[j])$ 其中節點 $j$ 分別對應到兩個 case。


不那麼顯然會有 :

$up[i] = w_{i,f} + max(D[f] , up[f] , max_{j \in child[f] , j \neq i}(down[j] + w_{j,f}) )$，$f$ 是 $i$ 的父節點; 簡而言之來源有父節點、祖先節點、兄弟節點。

$down[i] = max( max_{j \in child[i]}(down[j]) , max_{j\in child[i]}(D[j] + w_{i,j}))$。


以上，可以靈活的用 dfs 兩次就完成了。

但在 $up$ 的地方，若每次都把兄弟節點掃過一次，整體時間複雜度退化到 $O(N^{2})$，可以在處理 $down$ 的時候就紀錄最大、次大的，避免是一樣節點的狀況。

Time : $O(N)$

<br>

Code :
```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long 
#define pii pair<int,int>
#define F first
#define S second

int n;
vector<vector<array<int,2>>>g;
vector<int>D,up,down;
vector<pii>f,s;

void init(){
    g.resize(n);
    D.resize(n);
    up.resize(n);
    down.resize(n);
    f.assign(n,{-1,-1});
    s.assign(n,{-1,-1});
}

void dfs1(int v,int p){
    for(auto [u,w]:g[v]){
        if(u==p) continue;
        dfs1(u,v);
        int x = max(D[u],down[u]) + w;
        down[v] = max(x,down[v]);

        if(f[v].F==-1 || x > f[v].F){ s[v] = f[v]; f[v] = {x,u}; }
        else if(s[v].F==-1 || x > s[v].F){ s[v] = {x,u}; }
    }
}

void dfs2(int v,int p){
    for(auto [u,w] : g[v]){
        if(u==p) continue;

        up[u] = w + max({D[v],up[v],(u==f[v].S? s[v].F:f[v].F)});
        dfs2(u,v);
    }
}

signed main(){
    ios::sync_with_stdio(0),cin.tie(0);
    cin>>n;
    init();

    int A,B,C;
    for(int i=0;i<n-1;i++){
        cin>>A>>B>>C;
        A--; B--;
        g[A].push_back({B,C});
        g[B].push_back({A,C});
    }

    for(int i=0;i<n;i++) cin>>D[i];
    
    dfs1(0,0);
    dfs2(0,0);
    
    for(int i=0;i<n;i++)
        cout<<max(down[i],up[i])<<'\n';
    
    return(0);
}
```
