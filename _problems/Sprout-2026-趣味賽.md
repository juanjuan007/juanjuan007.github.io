---
layout: post
title: "趣味賽"
tags: ["Sprout-2026","list"]
---

---

### A. Ribbon

<br>

**思路**

首先，三點不共線可以確保任意三點皆不會形成退化三角形，也就是任意五點皆形成蝴蝶結。

再來，可以觀察到整張圖就是一棵樹，那麼「不可以重複經過任何通道」，代表那五個點一定在一條鍊上，否則要往回走。

實作上用 DFS 就可以了。

Time : $O(n)$

<br>

Code : 

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long 

int n , res = 0;
vector<int>w;
vector<vector<int>>g;

void dfs(int f,int v,vector<int> nw){
    nw.push_back(w[v]);
    if(nw.size() > 5) nw.pop_back();
    int sum = 0;
    if(nw.size()==5){
        for(int i : nw) sum += i;
        res = max(sum , res);
    }

    for(int u : g[v]){
        if(u==f) continue;
        dfs(v,u,nw);
    }
}

signed main(){
    cin>>n;
    w.resize(n);
    int x,y;
    for(int i=0;i<n;i++)
        cin>>x>>y>>w[i];
    g.resize(n);

    int u,v;
    for(int i=0;i<n-1;i++){
        cin>>u>>v;
        u--; v--;
        g[u].push_back(v);
        g[v].push_back(u);
    }
    dfs(-1,0,{});
    cout<<res;
    return(0);
}
```

---

### B. base10

<br>

**思路**


---
