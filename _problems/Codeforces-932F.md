---
layout: post
title: "932F-Escape Through Leaf"
tags: ["Codeforces"]
---

> 20260824

[Codeforces 932F](https://codeforces.com/problemset/problem/932/F)

**題敘**

給定一棵 $N$ 個節點的樹，每個節點有兩個權重 $a_{i}$ , $b_{i}$。

每個節點 $u$ 能往其子樹內的節點 $v$ 跳，花費是 $a_{u} \times b_{v}$。

求每個節點跳到葉節點的最小花費。

$1 \leq N \leq 2\times 10^{5}$

<br>

**思路**

我們令 $dp[v]$ 表節點 $v$ 跳到葉節點的最小花費， 那有 $dp[v] = min_{u \in subtree(v)}(dp[u] + a[v]\times b[u])$，計算順序顯然就是依照拓樸排序。

考慮對每個節點使用李超線段樹，我們將橫軸看成 $a$、縱軸看成 $dp$，每個節點的李超線段樹就是把其子節點的合併。

因為在每個點都開了一棵線段樹，所以我們要使用動態開點的技巧。

Time: $O(N \log N)$

<br>

Code :

特別注意除法是向 $0$ 取整，所以在有負數運算時，必須使用 `l + \frac{r-l,2}` 來計算。

```cpp
#include<bits/stdc++.h>
using namespace std;


#define int long long 

int cnt = 0;

const int SZ = 2e6;
int seg[4*SZ],chl[4*SZ],chr[4*SZ];

vector<int>slope,intercept;

int get(int id,int x){
    return(slope[id]*x + intercept[id]);
}

int Insert(int nw,int l,int r,int id){

    if(id==0) id = ++cnt;
    if(l==r){
        if(get(seg[id],l) > get(nw,l)) seg[id] = nw;
        return(id);
    }

    if(slope[seg[id]] > slope[nw]) swap(seg[id],nw);

    int m = l + (r-l)/2;
    if(get(seg[id],m) < get(nw,m)){
        chl[id] = Insert(nw,l,m,chl[id]);
    }else{
        swap(seg[id],nw);
        chr[id] = Insert(nw,m+1,r,chr[id]);
    }
    return(id);
}

int merge(int ida,int idb,int l,int r){
    if(ida==0) return(idb);
    if(idb==0) return(ida);

    if(l==r){
        if(get(seg[ida],l) < get(seg[idb],l)) return(ida);
        return(idb);
    }

    int m = l + (r-l)/2;
    chl[ida] = merge(chl[ida],chl[idb],l,m);
    chr[ida] = merge(chr[ida],chr[idb],m+1,r);

    Insert(seg[idb],l,r,ida);
    return(ida);
}

int query(int p,int l,int r,int id){
    if(l==r) return(get(seg[id],p));
    int m = l + (r-l)/2,res = 1e18;
    if(p<=m && chl[id]) res = query(p,l,m,chl[id]);
    else if(p>m && chr[id]) res = query(p,m+1,r,chr[id]);
    return(min(res,get(seg[id],p)));
}

vector<vector<int>>g;
vector<int>rt;


vector<int>a;

void dfs(int v,int f){
    int ch = 0;
    for(int u:g[v]){
        if(u==f) continue;
        ch++;
        dfs(u,v);
        rt[v] = merge(rt[v],rt[u],-SZ,SZ);
    }
    if(ch) intercept[v] = query(a[v],-SZ,SZ,rt[v]);
    rt[v] = Insert(v,-SZ,SZ,rt[v]);
}

signed main(){
    int n; cin>>n;

    a.resize(n+1); slope.resize(n+1); intercept.resize(n+1);
    intercept[0] = 1e18;
    
    for(int i=1;i<=n;i++) cin>>a[i];    
    for(int i=1;i<=n;i++) cin>>slope[i];

    g.resize(n+1);
    int u,v;
    for(int i=0;i<n-1;i++){
        cin>>u>>v;
        g[u].push_back(v);
        g[v].push_back(u);
    }
    rt.resize(n+1);
    dfs(1,1);
    for(int i=1;i<=n;i++) cout<<intercept[i]<<" ";
    return(0);
}
```
