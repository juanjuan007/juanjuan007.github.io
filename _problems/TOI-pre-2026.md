---
layout: post
title: "TOI-pre-2026"
tags: ["TOI"]
---

---

### D. 新高價

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

<br>

Subtask 2 : $n \leq 1000$

<br>

Subtask 3 : $k = 1$

<br>

Subtask 4 : $p_{i} = 1$

<br>

