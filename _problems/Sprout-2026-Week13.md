---
layout: post
title: "Week 13"
tags: ["Sprout-2026"]
---


### pB 167. 高棕櫚傳遞鏈

<br>

**思路**

輸出一張圖中所有割點。

Time : $O(n + m)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

int n , m , t = 0;
vector<vector<int>>g;

vector<int> vis , dfn , low , isAP;

void dfs(int fa , int v){
    dfn[v] = t; low[v] = dfn[v];
    vis[v] = 1;
    
    int ch = 0;
    for(int u:g[v]){
        if(u==fa) continue;
        if(vis[u]){ low[v] = min(dfn[u] , low[v]); continue; }
        ch++;
        t++; dfs(v,u); low[v] = min(low[u] , low[v]);
        if(fa!=-1 && low[u] >= dfn[v]) isAP[v] = 1;
    }

    if(fa==-1 && ch >= 2) isAP[v] = 1;
}

int main(){
    cin>>n>>m;
    g.resize(n);
    
    int u,v;

    for(int i=0;i<m;i++){
        cin>>u>>v;
        u--; v--;
        g[u].push_back(v);
        g[v].push_back(u);
    }
    
    vis.assign(n,0); dfn.resize(n); low.resize(n); isAP.assign(n,0);

    for(int i=0;i<n;i++)
        if(!vis[i]) dfs(-1,i);
    
    for(int i=0;i<n;i++)
        if(isAP[i]) cout<<i+1<<'\n';

    return(0);
}
```
