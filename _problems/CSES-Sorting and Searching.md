---
layout: post
title: "Sorting and Searching"
tags: ["CSES"]
---

<br>

#### Distinct Numbers

<br>

**題敘** 

給定一個長度為 $n$ 的陣列，計算陣列中不同數字的數量。

$1 \leq n \leq 2\times 10^{5}$

<br>

**思路**

用 set 維護。

<br>

Code : 

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    int n,x;
    cin>>n;
    set<int>s;
    for(int i=0;i<n;i++){
        cin>>x;
        s.insert(x);
    }
    cout<<s.size();
    return(0);
}
```

<br>

#### Apartments

<br>

**題敘**

有 $n$ 位客人、$m$ 
