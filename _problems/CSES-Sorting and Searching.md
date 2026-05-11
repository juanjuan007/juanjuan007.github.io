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

Time : $O(n\log n)$

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

### Apartments

<br>

**題敘**

有 $n$ 位客人、 $m$ 套公寓。你的目標是合理分配這些公寓，使盡可能多的客人都能獲得一套。

每位客人都有一個理想的面積 $b$ ，只要公寓面積 $a$ 與理想面積相差 $\leq k$ ，他們就會接受。

$1 \leq n,m \leq 2\times 10^{5}$ 、 $0 \leq k \leq 10^{9}$ 、 $1 \leq a_{i} , b_{i} \leq 10^{9}$

<br>

**思路**

先把 $b$ 排序，用貪心的方式來做選擇，每次都選擇可接受公寓中面積最小的。

<M一>

所以需要讓一個資料結構支援「新增」、「查找」、「刪除」 (剩餘公寓面積) ， 這可以使用 multiset ，因為不保證沒有重複值。

Time : $O(m \log m + m\log n)$

<br>

Code :

```cpp
#include<bits/stdc++.h>
using namespace std;

int main(){
    int n,m,k;
    cin>>n>>m>>k;
    
    multiset<int>a;
    int x;
    for(int i=0;i<n;i++){ cin>>x;a.insert(x); }

    vector<int>b(m);
    for(int i=0;i<m;i++)  cin>>b[i];
    sort(b.begin() , b.end());

    int cnt = 0;
    for(int i:b){
        auto it = a.lower_bound(i-k);
        if(it==a.end() || *it > i+k) continue;
        a.erase(it);
        cnt++;
    }
    cout<<cnt;
    return(0);
}
```
