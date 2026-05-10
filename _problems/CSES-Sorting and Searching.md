---
layout: post
title: "Sorting and Searching"
tags: ["CSES"]
---

<br>

### Distinct Numbers

<br>

**題敘** 

給定一個長度為 $n$ 的陣列，計算陣列中不同數字的數量。

$1 \leq n \leq 2\times 10^{5}$

<br>

**思路**

要讓資料結構內的東西不重複，所以可以用 set 維護。

<br>

Code : 

$O(n\logn)$

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

### Apartments

<br>

**題敘**

有 $n$ 位客人、 $m$ 套公寓。你的目標是合理分配這些公寓，使盡可能多的申請者都能獲得一套。

每位申請者都有一個理想的面積 $b$ ，只要公寓面積 $a$ 與理想面積相差 $\leq k$ ，他們就會接受。

$1 \leq n,m \leq 2\times 10^{5}$ 、 $0 \leq k \leq 10^{9}$ 、 $1 \leq a_{i} , b_{i} \leq 10^{9}$

<br>

**思路**

我們需要讓一個資料結構支援「新增」、「查找」、「刪除，並在高效
