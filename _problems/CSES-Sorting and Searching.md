---
layout: post
title: "Sorting and Searching"
tags: ["CSES"]
---

<br>

### Distinct Numbers

<br>

**題敘** 

給定一個長度為 $n$ 的陣列，求出陣列中不同數字的數量。

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

有 $n$ 位客人、 $m$ 套公寓。

每位客人都有一個理想的面積 $b$ ，只要公寓面積 $a$ 與理想面積相差 $\leq k$ ，他們就會接受。

求出最多有多少人能獲得一棟別墅。

$1 \leq n,m \leq 2\times 10^{5}$ 、 $0 \leq k \leq 10^{9}$ 、 $1 \leq a_{i} , b_{i} \leq 10^{9}$

<br>

**思路**

<M一>

當 $b$ 是無序時，若有數個可被選擇的公寓會無法做出選擇，因為你無法確保是否存在更優的取法。

因此先把 $b$ 排序，用貪心的方式來做選擇 : 每次都選擇可接受公寓中面積最小的。

所以需要讓一個資料結構支援「新增」、「查找」、「刪除」 (剩餘公寓面積) ， 這可以使用 multiset ，因為不保證沒有重複值。

Time : $O(m \log (m+n))$

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

<br>

<M二>

觀察上述思路，若我們將 $a$ 也排序，會發現選擇的公寓面積會單調不降，因為 $a_{i}-k \leq a_{i+1}-k$ 。

因此，可以用一個指針指向目前合法的最小公寓面積並不斷右移。

Time : $O(n \log n + m \log m)$ ， 常數較 <M一> 小


<br>

Code

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int n,m,k;
    cin>>n>>m>>k;
    vector<int>a(n);
    vector<int>b(m);
    for(int i=0;i<n;i++) cin>>a[i];
    for(int i=0;i<m;i++) cin>>b[i];
    sort(a.begin(),a.end());
    sort(b.begin(),b.end());
    int ans=0,p1=0,p2=0;
    for(;p1<n;p1++){
        while(p2<m && !(a[p1]-k <= b[p2] && b[p2] <= a[p1]+k)){
            if(a[p1]+k < b[p2]) break;
            p2++;
        }
        if(p2<m && a[p1]-k <= b[p2] && b[p2] <= a[p1]+k){
            ans++;
            p2++;
        }
    }
    cout<<ans;
    return(0);
}
```

<br>

### Ferris Wheel

<br>

**題敘**

有 $n$ 個人去坐摩天輪，第 $i$ 個人重 $p_{i}$。

每個吊廂可以容納至多二人，且總重至多 $x$。

求出最少需要幾個吊廂使所有人都坐到。

$1 \leq n \leq 2\times 10^{5}$ 、 $1 \leq x \leq 10^{9}$ 、 $1\leq p_{i} \leq x$

<br>

**思路**

應該可以想到貪心策略 : 目前最重的人，如果可以就搭配目前最輕的。 

而上述這件事只要先將 $p$ 排序，再使用雙指針指向當前最小、最大值即可。

那我們可以體會一下這個作法的正確性。

> 假設存在一個最佳解 $S$
> 
> case 1 : $p_{l} + p_{r} \leq x$
> 
> 如果在 $S$ 中並非如此，會有以下二種狀況 :
> 
> - 第 $r$ 人自己一個吊廂
  那把第 $l$ 人加入也是合法的，且第 $l$ 人離開原車廂後重量減少必然合法。
>
> - 第 $r$ 人跟另一人一個吊廂
  假設在 $S$ 中， 分配吊廂如 : ${l , m}$ , ${k , r}$ $(m \ne k)$
  那此時交換形成 ${l,r}$ , ${m,k}$ ，前者易知，後者可由 $p_{m} + P_{k} \leq p_{k} + p_{r} \leq x$ 說明合法。
> 
> case 2 : $p_{l} + p_{r} > x$
> 
> 那代表對於任意的 $m > l$ ， 也不會滿足 $p_{m} + p_{r} \leq x$ ， 所以第 $r$ 個人自己一個吊廂是必然的，在 $S$ 中也是如此。
>
>  因可在不會變差的情況下，透過交換最優解 $S$ 形成我們的貪心作法，故可證同為最佳解。

Time : $O(n \log n)$

<br>

Code

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    int n,x;
    cin>>n>>x;
    vector<int>p(n);
    for(int i=0;i<n;i++) cin>>p[i];
    sort(p.begin(),p.end());
    int ans = 0,l = 0,r = n-1;
    while(l<=r){
        if(p[l] + p[r] <= x){ l++;r--; }
        else r--;
        ans++;
    }
    cout<<ans;
    return(0);
}
```

<br>

### Concert Tickets

<br>

**題敘**





