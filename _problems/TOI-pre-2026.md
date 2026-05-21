---
layout: post
title: "TOI-pre-2026"
tags: ["TOI"]
---

---

### D. 新高價

Subtask 1
```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long 

int n,L;
vector<int>p;

int f(int l,int r){
    int res = 0;
    int mx = p[l];

    for(int i=l+1;i<=r;i++){
        if(p[i] > mx){
            res += p[i];
            mx = p[i];
        }
    }

    return res;
}

signed main(){
    cin>>n>>L;
    p.resize(n);
    for(int i=0;i<n;i++) cin>>p[i];
    
    for(int i=0;i<n;i++){
        int nw = 0;
        for(int j=0;j<n;j++)
            nw += f(j,min(j+i , n-1));
            
        if(nw > L){
            cout<<i-1;
            return(0);
        }
    }
    cout<<n-1;
    return(0);
}
```

Substask 3 : $p_{1} < p_{2} < ... < p_{n}$

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long 

int n,L;
vector<int>p , perfix;


int get(int l , int r){
    r = min(n-1 , r);
    if(l==0) return(perfix[r]);
    return(perfix[r] - perfix[l-1]);
}

bool check(int x){
    int res = 0;
    for(int i=0;i<n;i++) res += get(i+1,i+x);
    return(res <= L);
}

signed main(){
    cin>>n>>L;
    p.resize(n);
    for(int i=0;i<n;i++) cin>>p[i];
    
    perfix = p;
    for(int i=1;i<n;i++) perfix[i] += perfix[i-1];
    
    int res , l = 0, r = n-1 , m;
    
    while(l<=r){
        m = (l+r)/2;
        if(check(m)){
            res = m;
            l = m+1;
        }else{
            r = m-1;
        }
    }
    cout<<res;    
    return(0);
}
```

滿分解

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int unsigned long long 

int n,M;
vector<int>p,pre;

bool check(int x){
    int  nw = 0;
    for(int i=0;i<n;i++)
        nw += p[i] * min(i - pre[i] - 1, x);
    return(nw <= M);
}

signed main(){
    cin>>n>>M;
    p.resize(n);
    for(int i=0;i<n;i++) cin >> p[i];
    pre.resize(n);
    stack<int> st;
    for(int i=0;i<n;i++){
        while(!st.empty() && p[st.top()] < p[i]) st.pop();
        pre[i] = (st.empty() ? -1 : st.top());
        st.push(i);
    }

    int res = 0;
    int l = 0 , r = n-1;
    while(l <= r){
        int m = (l+r)/2;
        if(check(m)){
            res = m;
            l = m+1;
        }else{
            r = m-1;
        }
    }
    cout<<res; 
    return(0);
}
```

---

### E.幾乎獨立的分店 (independent)

<br>

**思路**

<br>

Subtask 1 : $n \leq 20$

每個節點只有兩種可能 : 有開分店或沒開分店。

所以用 01 枚舉所有可能的狀況，再計算能得到的利益，取最大值即可。

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long 

int n , m , ans;
vector<int>w;
vector<vector<int>>g;

void f(int x){
    int nw = 0;
    for(int i=0;i<n;i++){
        if(!(x & (1<<i))) continue;
        nw += w[i]; 
        int cnt = 0;
        for(int j : g[i]){
            if(x & (1<<j)) cnt++;
        }
        if(cnt > m) return;
    }
    ans = max( nw , ans);
    return;
}

signed main(){
    ios::sync_with_stdio(0) , cin.tie(0);
    
    cin>>n>>m;
    
    w.resize(n);
    for(int i=0;i<n;i++) cin>>w[i];
    
    g.resize(n);
    
    int u,v;
    for(int i=0;i<n-1;i++){
        cin>>u>>v;
        u--; v--;
        g[u].push_back(v);
        g[v].push_back(u);
    }    
    
    for(int i=0;i<(1<<n);i++) f(i);
    
    cout<<ans;

    return(0);
}
```

<br>

Subtask 2 : $n \leq 1000$

<br>

Subtask 3 : $k = 1$

<br>

Subtask 4 : $p_{i} = 1$

<br>

