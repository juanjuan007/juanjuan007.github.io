---
layout: post
title: "CH2-前綴和、差分與離散化"
tags: ["洛谷","list"]
---

---

### 求區間和

[Luogu P8218](https://www.luogu.com.cn/problem/P8218)

<br>

**思路**

用前綴和維護。

Time : $O(n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
signed main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int n;
    cin>>n;
    vector<int>num(n),p(n+1);
    for(int i=0;i<n;i++){
        cin>>num[i];
        p[i+1] = p[i]+num[i];
    }
    int m;
    cin>>m;
    int a,b;
    for(int i=0;i<m;i++){
        cin>>a>>b;
        a--; b--;
        cout<<p[b+1]-p[a]<<'\n';
    }
    return(0);
}
```

---

### 
