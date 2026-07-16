---
layout: post
title: "pre-2026"
tags: ["TOI"]
---

### A. 

簽到題


---

### D. 新高價

<br>

**題敘**

某個投資競賽進行 $n$ 秒，第 $i$ 秒的價格為 $p_{i}$。

共有 $n$ 參賽者，第 $i$ 位參賽者，會在第 $i$ 秒以 $p_{i}$ 的價格買入，

若在某一秒 $t$，滿足 $i < t \leq min(i+R,n)$ 且  $p_{t} > max{p_{i} , p_{i+1} , ... , p_{t-1}}$，則稱對第 $i$ 位參加 $t_{i}$ 是一個新高價，

**思路**

<br>

subtask 1 : $n \leq 200$

Time : $O(n^3)$

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

<br>

subtack 2 : $n \leq 5000$
Time : $O(n^{2} \log n)$

Code:
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

int check(int i){
    int nw = 0;
    for(int j=0;j<n;j++)
        nw += f(j,min(j+i , n-1));
    return(nw <= L);  
}

signed main(){
    cin>>n>>L;
    p.resize(n);
    for(int i=0;i<n;i++) cin>>p[i];
    
    int res,l=0,r=n-1,m;
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

<br>

subtask 3 : $p_{1} < p_{2} < ... < p_{n}$

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


<br>

滿分解

Code :
```cpp
#include<bits/stdc++.h>
using namespace std;


#define int long long 
#define pr pair<int,int>
#define F first
#define S second

int n,L,LG;
vector<int>p;
vector<vector<pr>>up;

void init(){
    LG = __lg(n)+1;
    up.resize(n,vector<pr>(LG+1));
    stack<int>st;
    for(int i=n-1;i>=0;i--){
        while(!st.empty() && p[i] >= p[st.top()]) st.pop();
        if(st.empty()){ up[i][0].F = n; up[i][0].S = 0; }
        else{ up[i][0].F = st.top(); up[i][0].S = p[st.top()]; }

        st.push(i);
    }

    for(int i=1;i<=LG;i++){
        for(int j=0;j<n;j++){
            if(up[j][i-1].F == n){
                up[j][i].F = up[j][i-1].F;
                up[j][i].S = up[j][i-1].S;
            }else{
                up[j][i].F = up[up[j][i-1].F][i-1].F;
                up[j][i].S = up[j][i-1].S + up[up[j][i-1].F][i-1].S;
            }
        }
    }
    return;
}

int get(int l,int r){
    int res = 0;
    for(int j=LG;j>=0;j--){
        if(up[l][j].F > r) continue;
        res += up[l][j].S;
        l = up[l][j].F;
    }
    return(res);
}

bool check(int R){
    int nw = 0;
    for(int i=0;i<n;i++)
        nw += get(i,min(i+R,n-1));
    return(nw <= L);
}


signed main(){
    cin>>n>>L;
    p.resize(n);
    for(int i=0;i<n;i++) cin>>p[i];
    init();

    int res,l=0,r=n-1,m;
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

Code : 

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


Code : - By PCC
```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long 

int n,L;
vector<int>p;

bool check(int R){
    int res = 0,nw = 0;
    deque<int>dq;
    for(int i=n-1;i>=0;i--){
        while(!dq.empty() && dq.front() > i+R){ nw-= p[dq.front()]; dq.pop_front(); }
        while(!dq.empty() && p[i] >= p[dq.back()]){ nw-= p[dq.back()]; dq.pop_back(); }
        res += nw;
        dq.push_back(i); nw += p[i]; 
    }
    return(res <= L);
}

signed main(){
    cin>>n>>L;
    p.resize(n);
    for(int i=0;i<n;i++) cin>>p[i];
    
    int res ,l=0,r=n-1,m;
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

