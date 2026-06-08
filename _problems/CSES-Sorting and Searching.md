---
layout: post
title: "Sorting and Searching"
tags: ["CSES"]
---

---

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

---

### Apartments

<br>

**題敘**

有 $n$ 位客人、 $m$ 套公寓。

每位客人都有一個理想的面積 $b$ ，只要公寓面積 $a$ 與理想面積相差 $\leq k$ ，他們就會接受。

求出最多有多少人能獲得一棟別墅。

$1 \leq n,m \leq 2\times 10^{5}$ 、 $0 \leq k \leq 10^{9}$ 、 $1 \leq a_{i} , b_{i} \leq 10^{9}$

<br>

**思路**

<br>

&lt;M一&gt;

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

&lt;M二&gt;

觀察上述思路，若我們將 $a$ 也排序，會發現選擇的公寓面積會單調不降，因為 $a_{i}-k \leq a_{i+1}-k$ 。

因此，可以用一個指針指向目前合法的最小公寓面積並不斷右移。

Time : $O(n \log n + m \log m)$ ， 常數較 &lt;M一&gt; 小


<br>

Code :

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

---

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

Code :

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

---

### Concert Tickets

<br>

**題敘**

有 $n$ 張門票可供選擇，第 $i$ 張的價格是 $h_{i}$。
會有 $m$ 個顧客接續到來，每位顧客會有一個 $t_{i}$，表能接受的最高票價，他會獲得離最高票價最接近的車票，求出所獲得票價，若無法獲得即是 $-1$。

$1 \leq n,m \leq 2\times 10^{5}$ 、 $1\leq h_{i} , t_{i} \leq 10^{9}$

<br>

**思路**

每次要找當前有的票中價格 $\leq t$ 的最大者。

<br>

&lt;M一&gt;

若價格有序，獲得的車票等價於第一個價格 $>t$ 左側那張。

可以使用 multiset 跟二分搜來完成。

Time : $O((n+m) \log n)$

<br>

Code :

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int n,m,x;
    cin>>n>>m;
    multiset<int>s;
    for(int i=0;i<n;i++){
        cin>>x;
        s.insert(x);
    }
    for(int i=0;i<m;i++){
        cin>>x;
        auto it = s.upper_bound(x);
        if(it==s.begin()){
            cout<<-1<<'\n';
        }else{
            it--;
            cout<<*it<<'\n';
            s.erase(it);
        }
    }
    return(0);
}
```

<br>

&lt;M二&gt;

若價格有序，我們只需要先找到最大符合的價格(方法略同上)，並持續向左直到當前價格尚未被買走，換而言之也就是要找左側最近(含自己)尚未被買走的票。

這件事可以用 DSU 來做到。

Time : $O((n+m) \log n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
 
int n;
vector<int>f,sz;
 
void init(){
    f.resize(n);
    iota(f.begin(),f.end(),0);
}
 
int find_root(int x){
    if(f[x]==-1) return(-1);
    if(f[x]==x) return(x);
    return(f[x] = find_root(f[x]));
}
 
int main(){
    int m,t;
    cin>>n>>m;
 
    init();
 
    vector<int>h(n);
    for(int i=0;i<n;i++) cin>>h[i];
    sort(h.begin(),h.end());
 
    for(int i=0;i<m;i++){
        cin>>t;
        if(t < h[0]){ cout<<-1<<'\n'; continue;} 
        int it = upper_bound(h.begin(),h.end(),t) - h.begin() - 1;
        int buy = find_root(it);
        if(buy==-1){
            cout<<-1<<'\n';
        }else{
            cout<<h[buy]<<'\n';
            if(buy==0) f[buy] = -1;
            else f[buy] = f[buy-1];
        }
    }
    return(0);
}
```

---

### Restaurant Customers

<br>

**題序**

總共有 $n$ 位客人到餐廳，每個人皆有進入、離開時間 $[a,b]$，求出餐廳內最多同時有幾人。

$1\leq n \leq 2\times 10^{5}$ 、 $1 \lea a < b \leq 10^{9}$

<br>

**思路**

在每個時間點要知道幾個人進出，而不在乎是哪個人。

我們可以依序枚舉時間並更新當前有幾個人，這樣是 $O(10^{9})$ 會 TLE ，

可以發現若有一段時間沒有進出那總人數是不變的，根本不需要計算，我們只需要關注有人進出的時間即可。

可以把進入、離開的時間分別排序，每次選擇兩者前面較小的一個做處理，整體概念與雙指針類似。

Time : $O(n \log n)$

<br>

Code : 

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    int n;
    cin>>n;
    vector<int>in(n),out(n);
    for(int i=0;i<n;i++) cin>>in[i]>>out[i];
    sort(in.begin(),in.end());
    sort(out.begin(),out.end());
    int ans = 0,nw = 0,a = 0, b = 0;
    for(;a<n;a++){
        while(b<n && out[b] < in[a]){ nw--; b++; }
        nw++;
        ans = max(ans,nw);
    }
    cout<<ans;
    return(0);
}
```

---
