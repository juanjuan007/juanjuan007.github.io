---
layout: post
title: "pre-2026"
tags: ["TOI"]
---


[補題 OJ](https://codeforces.com/gym/677534)  by AA競程

---

### A. 

簽到題


---

### D. 新高價

<br>

**題敘**

某個投資競賽進行 $n$ 秒，第 $i$ 秒的價格為 $p_{i}$。

共有 $n$ 參賽者，第 $i$ 位參賽者，會在第 $i$ 秒以 $p_{i}$ 的價格買入，

若在某一秒 $t$，滿足 $i < t \leq min(i+R,n)$ 且  $p_{t} > max{p_{i} , p_{i+1} , ... , p_{t-1}}$，則稱對第 $i$ 位參加 $t_{i}$ 是一個新高價，就可以獲得該價格的獎金。

若所有參賽者獲得的獎金總和不能超過預算 $L$。求出最大的整數 $R \leq n-1$ 滿足條件。

<br>

**思路**

- subtask 1 : $n \leq 200$

暴力枚舉 $R$，依序處理每個人遇到的新高價。

Time : $O(n^3)$

<br>

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

    return(res);
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

- subtack 2 : $n \leq 5000$

可以觀察到: $R$ 遞增時，獲得的總獎金也是遞增的。

由於存在單調性，因此考慮二分搜 $R$。
  
Time : $O(n^{2} \log n)$

<br>

Code:

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long 

int n,L;
vector<int>p;

int f(int l,int r){
    int res = 0 , mx = p[l];

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

- subtask 3 : $p_{1} < p_{2} < ... < p_{n}$

由於 $p$ 是遞增的，那代表每一個位置都是新高價。

所以在 $i$ 所獲得的獎金會是 $\sum_{x = 1}^{R} p[i+x]$，那就可以用前綴和來維護這件事。

Time: $O(n \log^{2} n)$

<br>

Code: 

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

- 滿分解

&lt;M一&gt;

不妨考慮使用倍增法，跳到下 2的冪次 個新高價並維護總和。下 $2^{0}$ 個，是用 monotonic stack 做到。

那在二分搜 $R$ 後，只要在每個位置，在上述的倍增表格中一次二分搜即可。

Time : $O(n \log^{2} n)$

<br>

Code :
```cpp
#include<bits/stdc++.h>
using namespace std;


#define int long long 
#define pii pair<int,int>
#define F first
#define S second

int n,L,LG;
vector<int>p;
vector<vector<pii>>up;

void init(){
    LG = __lg(n)+1;
    up.resize(n,vector<pii>(LG+1));
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


<br>


&lt;M二&gt;

可以觀察: 在位置 $i$ 對那些位置而言是新高價，

易知，若其左側最近較大值的索引值是 $pre[i]$，那麼對於 $(pre[i],[i])$，$p[i]$ 皆是新高價，這可以用 monotonic stack 做到。

所以，二分搜再計數即可。

Time: $O(n \log n)$ 


<br>

Code: 

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

<br>

&lt;M三&gt;

By PCC

從右到左，用 monotonic deque 維護當前是新高價者。

一樣需要二分搜 $R$ 才能銜接上述的事情。

Time: $O(n \log n)$

<br>

Code:
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

[更多做法](https://hackmd.io/@alan-huang-82789/TOI2026ST_pD) by huangallen

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

