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

那我們可以體會一下這個作法的證明。

> p.f.
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

### Movie Festival

<br>

**題敘**

有 $n$ 部電影，每部有開始、結束時間 $[a,b]$ ，求出最多可以看幾部電影。

$1 \leq n  \leq 2\times 10^{5}$ 、 $1 \leq a < b \leq 10^{9}$

<br>

**思路**

若把每部電影撥放時間視為一個線段，也等價於要找最多不重疊線段數。

這是非常經典的貪心問題，解法則是 : 優先選右端點靠左的線段,如果會重疊到選過的線段就不選。

很難想到這種思路，不過一樣可以來體會證明。

> p.f.
> 假設按照我們的做法是 $S$，有一個最佳解 $S'$。
>
> 兩者都先照右端點排序。
> 
> 找到第一個不相同的線段 $S_{i}$ , $S'_{i}$，
>
> 因為前者的右端點較左，因此把 $S'_{i}$ 替換成 $S_{i}$ 不會影響其它段，同時也會是最佳解之一。
>
> 透過上述不斷操作，我們能將 $S'$ 在不改變是最優解的同時換成 $S$ ，故可證 $S$ 是最佳解。
>

Time : $O(n \log n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

#define p pair<int,int>
#define F first
#define S second
 
int main(){
    int n;
    cin>>n;
    vector<p>x(n);
    for(int i=0;i<n;i++) cin>>x[i].S>>x[i].F;
    sort(x.begin(),x.end());
    int ans = 0,nw = -1;
    for(auto [ed,st] : x){
        if(st >= nw){
            ans++;
            nw = ed;
        }
    }
    cout<<ans;
    return(0);
}
```

--- 

### Sum of Two Values

<br>

**題敘**

在一個有 $n$ 個數的陣列 $a$ 中，求出兩個不同位置的數字和為 $x$。

$1 \leq  n \leq 2\times 10^{5}$ 、 $1 \leq x , a_{i} \leq 10^{9}$

<br>

**思路**

利用排序使得陣列有單調性，

可以觀察到兩件事 : 

若 $a_{l} + a_{r} > x$，則對於任意的 $r' > r$ ， $a_{l} + a_{r'} > x$ 是必然的。

若 $a_{l} + a_{r} < x$，則對於任意的 $l' < l$ ， $a_{l'} + a_{r} < x$ 也是必然的。

就會發現暴力法中其實會算到很多不必要的組合。

可以用兩個指針 $l$ , $r$ 來維護上面這件事。

當 $a_{l} + a_{r} < x$ 時，右移左指針 ; 當 $a_{l} + a_{r} > x$ 時，左移右指針。

大致原因如上，也可以想成在移除某個元素時，代表不可能找到另一個元素與其符合。

Time : $O( n\log n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long
#define p pair<int,int>
#define F first
#define S second

signed main(){
    int n,k,x;
    cin>>n>>k;
    vector<p>num(n);
    for(int i=0;i<n;i++){
        cin>>x;
        num[i] = {x,i+1};
    }
    sort(num.begin(),num.end());
    int l = 0,r = n-1;
    while(l < r){
        if(num[l].F + num[r].F == k){
            cout<<num[l].S<<" "<<num[r].S;
            return(0);
        }else if(num[l].F + num[r].F < k){
            l++;
        }else{
            r--;
        }
    }
    cout<<"IMPOSSIBLE";
    return(0);
}
```

---

### Maximum Subarray Sum

<br>

**題敘**

在一個有 $n$ 個數的陣列 $x$ 中，求出最大子陣列和(不接受空陣列)。

$1 \leq  n \leq 2\times 10^{5}$ 、 $-10^{9} \leq x_{i} \leq 10^{9}$

<br>

**思路**

這題有 dp 的味道，

令 $dp[i]$ 代表以當前結尾最大子陣列和，那麼顯然有 $dp[i] = max(dp[i-1] + x[i] , x[i])$ ，也就是取或不取前面的。

觀察轉移只需要前面一項，所以可以用滾動的方式，也就是開一個變數紀錄即可，又稱作 Kadane's Algorithm。

Time : $O(n)$

<br>

Code : 

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long 

signed main(){
    int n;
    cin>>n;

    int ans = -1e9 , pre = 0 , nw;
    vector<int>x(n);
    for(int i=0;i<n;i++){
        cin>>x[i];
        nw = max(pre,0LL) + x[i];
        ans = max(nw , ans);
        pre = nw;
    }
    cout<<ans;
    return(0);
}
```

---

### Stick Lengths

<br>

**題敘**

有 $n$ 根木棍，每根都有長度 $p$，你要修剪木棍長度使其一致，縮短或加長的代價都是與原本的長度差。求最小代價。

$1\leq n \leq 2\times10^{5}$ 、 $1 \leq p_{i} \leq 10^{9}$

<br>

**思路**

假設最終長度是 $x$ ， 那總代價會是 \sum_{i = 1}^{n} |x - p_{i}|。

可以發現 $x$ 是中位數時，總代價最小。

體會一下這個做法，

假設有兩個點 $A$ , $B$，若取的 $x$ 在 \overline{\rm AB} 上顯然最優，那我們分別對最左、最右的兩點做配對就可以得出上述推論。

至於找中位數，可以直接排序 $p$ 即可。

Time : $O(n \log n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long

signed main(){
    int n;
    cin>>n;
    vector<int>p(n);
    for(int i=0;i<n;i++) cin>>p[i];
    sort(p.begin(),p.end());
    int m = n/2; m = p[m];
    int ans = 0;
    for(int i:p) ans+=abs(m-i);
    cout<<ans;
    return(0);
}
```

---

### Missing Coin Sum

<br>

**題敘**

共有 $n$ 個硬幣，每個都有其面額 $x$，求出最小的價格無法用某部分硬幣駔成。

$1 \leq n \leq 2\times 10^{5}$ 、 $1 \leq x_{i} \leq 10^{9}$

<br>

**思路**

我們維護一個子序列，滿足可以湊出任意金額 $\leq nowTotal (總和)$，並透過增加硬幣使範圍擴張。

要加入一枚硬幣 $i$ ，必須滿足 $c_{i} \leq nowTotal + 1$，否則會無法湊出 $nowTotal + 1$。

可以發現由小到大依序加入是最優的策略，因為要盡可能增大 $nowTotal$ ，提高較大的面額加入的可能。

Time : $O(n \log n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
signed main(){
    int n;
    cin>>n;
    vector<int>x(n);
    for(int i=0;i<n;i++) cin>>x[i];
    sort(x.begin(),x.end());
    int nw = 0;
    for(int i : x){
        if(i > nw+1) break;
        nw += i;
    }
    cout<<nw+1;
    return(0);
}
```

---

### Collecting Numbers

<br>

**題敘**

在一個陣列 $x$ 中有 $n$ 個數字，$1 ,2 ,... , n$ 恰出現一次。

要按照大小順序收集所有數字。 在每一輪中，只能左至右收集，求要幾輪才能完成。

<br>

**思路**

可以觀察到要收集每個數字只與前一個數字有關，

若位置在前一個數字位置右側，則可以直接收集 ; 反之，則需要再多一輪。

注意答案初始值就是 $1$ 輪。

Time : $O(n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

int main(){
    int n;
    cin>>n;
    vector<int>x(n) , id(n);
    for(int i=0;i<n;i++){
        cin>>x[i];
        id[x[i]-1] = i;
    }

    int ans = 1;
    for(int i=1;i<n;i++)
        if(id[i] < id[i-1]) ans++;
    
    cout<<ans;
    return(0);
}
```

---

### Collecting Numbers II

<br>

**題敘**

在一個陣列 $x$ 中有 $n$ 個數字，$1 ,2 ,... , n$ 恰出現一次。

其中會有 $m$ 次操作，$(a,b)$ 要把陣列中第 $a$ 個、第 $b$ 個數字交換，

在每次交換完後，要按照大小順序收集所有數字。 在每一輪中，只能左至右收集，求要幾輪才能完成。

$1 \leq n , m \leq 2 \times 10^{5}$ 、 $1\leq a,b \leq n$

<br>

**思路**

本質上跟前一題類似。

對於每個交換，要先扣掉尚未交換前的，再加上後來的。

可以注意到若兩者相鄰，就會重複多算，因此用 set 來去重是一個好方法。 

Time : $O(n + m)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long 
signed main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int n,m;
    cin>>n>>m;
    vector<int>x(n),p(n);
    
    for(int i=0;i<n;i++){
        cin>>x[i];
        p[x[i]-1] = i;
    }
    
    int nw = 1;
    for(int i=1;i<n;i++)
        if(p[i] < p[i-1]) nw++;
    
    int a,b,valA,valB;
    
    while(m--){
        cin>>a>>b;
        a--; b--;
        
        valA = x[a]-1; valB = x[b]-1;
            
        set<pair<int,int>>s;
        if(valA) s.insert({valA-1,valA});
        if(valA+1<n) s.insert({valA,valA+1});
        if(valB) s.insert({valB-1,valB});
        if(valB+1<n) s.insert({valB,valB+1});
        
        for(auto [u,v] : s)
            if(p[u] > p[v]) nw--;
            
        swap(x[a],x[b]);
        swap(p[valA],p[valB]);
        
        for(auto [u,v]:s)
            if(p[u] > p[v]) nw++;
            
        cout<<nw<<'\n';
    }
    return(0);
}
```

---

### Playlist

<br>

**題敘**

有一個電視台的撥放清單 $k$ ，總共有 $n$ 首歌。

求最長的連續歌曲滿足每有重複歌曲的長度。

$1 \leq n \leq 2 \times 10^{5}$ 、 $1 \leq k_{i} leq 10^{9}$

<br>

**思路**

可以依次處理每個位置是滿足子陣列右端點的可能，從中找最大值就是答案。

我們可以發現 : 當右端點遞增時，左端點也是單調不降的。

可以用雙指針維護。當右端點括張時，不斷縮小左端點直到符合條件。

Time : $O(n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    int n;
    cin>>n;
    vector<int>x(n);
    for(int i=0;i<n;i++) cin>>x[i];
    map<int,int>mp;
    int ans = 1,l = 0,r = 0;
    while(r < n){
        mp[x[r]]++;
        while(mp[x[r]]>1){
            mp[x[l]]--;
            l++;
        }
        ans = max(r-l+1,ans);
        r++;
    }
    cout<<ans;
    return(0);
}
```

---

### Towers

<br>

**題敘**

有 $n$ 個積木，每個積木都有一個體積 $k$，要疊成數座塔。一個積木要疊上那座塔，體積必須小於其頂端體積。

要按照順序處理每個積木，可以堆上去某座塔或是新建造一座。求至少要幾座。

$1 \leq n \leq 2\times 10^{5}$ 、 $1 \leq k_{i} \leq 10^{9}$

<br>

**思路**

應該很容易可以想到 : 當處理到一個積木，要放在「能放的塔中頂端積木體積最小的」。

應該不難理解，就是如果你放在其他座就會大幅減少後面的可能。但可以嘗試證明 : 

> p.f 
> 
> 假設存在一個最佳解 $S$，
> 在處理第 $i$ 個積木時，依照上述做法應該放在第 $x$ 座，其頂端體積為 $k_{a}$，但在 $S$ 中放在第 $y$ 棟，其頂端體積為 $k_{b}$
> 由上述作法易知 $k_{a} \leq k_{b}$，所以將 $x$ , $y$ 兩座上面全部交換也是合法的。
> 透過在不變壞的前提下不斷交換，可以將最佳解改成上述作法，故可證同為最佳解。
> 

可以使用 multiset + 二分搜來達成。

Time : $O(n \log n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

int main(){
    int n;
    cin>>n;

    int k;
    multiset<int>s;

    for(int i=0;i<n;i++){
        cin>>k;
        auto it = s.upper_bound(k);
        if(it!=s.end()) s.erase(it);
        s.insert(k);
    }
    cout<<s.size();
    return(0);
}
```


---

### Traffic Lights

<br>

**題敘**

有一段長 $x$ 的道路，位置編號是 $[0,x]$，最初沒有交通號誌，會有 $n$ 個號誌依序加入，會被放在 $p_{i}$。

求每次加入交通號誌後，最長無交通號誌路段的長度。

$1 \leq x \leq 10^{9}$ 、 $1\leq n \leq 2\times 10^{5}$ 、 $0 < p_{i} < x$

<br>

**思路**

每次新加入一個，就會把一段無交通號誌的路徑切斷，而那條路徑的端點顯然是前、後一個交通號誌，就可以把長度算出來。

可以用 multiset 維護當前所有路徑長度，並用 set 維護有交通號誌的位置。新加入交通號誌時，只需要搭配二分搜等操作即可。

為了方便，可以想像初始狀態是 -1 , n 有交通號誌的情況。

Time : $O(n \log n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
 
int main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int x,n;
    cin>>x>>n;
    set<int>s1;
    s1.insert(0); s1.insert(x);
    
    multiset<int>s2;
    s2.insert(x);
    
    int p;
    for(int i=0;i<n;i++){
        cin>>p;
        auto a = s1.upper_bound(p),b = a; b--;
        int d = *a - *b;
        s2.erase(s2.find(d));
        s2.insert(*a - p);
        s2.insert(p- *b);
        s1.insert(p);
        cout<<*s2.rbegin()<<' ';
    }
    return(0);
}
```

---

### Distinct Values Subarrays

<br>

**題敘**

一個長度為 $n$ 的陣列 $x$。求每個元素都不相同的子陣列數量。

$1\leq n \leq 2\times 10^{5}$ 、 $1 \leq x_{i} \leq 10^{9}$

<br>

**思路**

子陣列是要連續的，考慮一段長度為 $x$ 的陣列中沒有相同元素，那以最後一個為右端點的子陣列有 $x$ 種，注意也等價於每個元素都不相同的子陣列。

那可以枚舉每個位置是子陣列右端點的可能，取總和即可。會發現左端點是不降的，因而用雙指針維護。

Time : $O(n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
signed main(){
    int n;
    cin>>n;
    vector<int>x(n);
    for(int i=0;i<n;i++) cin>>x[i];
    int l = 0,r = 0,ans = 0;
    map<int,int>mp;
    while(r < n){
        mp[x[r]]++;
        while(mp[x[r]] > 1) mp[x[l++]]--;
        ans += r-l+1;
        r++;
    }
    cout<<ans;
    return(0);
}
```

---

### Distinct Values Subsequences

<br>

**題敘**

一個長度為 $n$ 的陣列 $x$。求每個元素都不相同的子序列數量。

$1\leq n \leq 2\times 10^{5}$ 、 $1 \leq x_{i} \leq 10^{9}$

<br>

**思路**

對於一個元素 $x_{i}$，假設它出現 $k$ 次，那對於這個數字的所有可能有 : 沒、第一個、第二個、第三個、...、第 $k$ 個出現，共 k+1 種。

把每個數字的出現可能相乘就是答案，記得 -1 : 空陣列的狀況。

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long

const int MOD = 1e9+7;

signed main(){
    int n,x,ans = 1;
    cin>>n;
    set<int>s;
    map<int,int>mp;
    for(int i=0;i<n;i++){
        cin>>x;
        s.insert(x);
        mp[x]++;
    }
    for(int i:s){
        ans = (ans*(mp[i]+1))%MOD;
    }
    cout<<(ans-1+MOD)%MOD;
    return(0);
}
```

---

### Josephus Problem I

<br>

**題敘**

$n$ 個人圍成一圈，編號為 $[1,n]$，從編號 1 開始，每隔一個人就離開圓圈。

求離開圓圈順序。

$1\leq n \leq 2\times 10^{5}$

<br>

**思路**

可以觀察到每繞一圈，人數就會減半，所以至多需要 $\log_{2} n$ 輪。

可以直接使用 queue 模擬。

Time : $O(n \log n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    int n;
    cin>>n;
    queue<int>que;
    for(int i=1;i<=n;i++) que.push(i);
    while(!que.empty()){
        que.push(que.front());
        que.pop();
        cout<<que.front()<<" ";       
        que.pop();
    }
    return(0);
}
```

### Josephus Problem II

<br>

**題敘**

$n$ 個人圍成一圈，編號為 $[1,n]$，從編號 1 開始，每隔 $x$ 個人就離開圓圈。

求離開圓圈順序。

$1\leq n \leq 2\times 10^{5}$ 、 $1 \leq x \leq 10^{9}$

<br>

**思路**

不幸的是用 queue 模擬行不通， 當 $x > n$ 時，達到 $O(n \times \frac{k}{n})$ 會 TLE 是必然。

當現在在位置 $p$ 的人離開圓圈，我們要有效率地找還在圓圈內的下 $x$ 個。

在處理時，若圓圈內有 $k$ 個人，若 $x > k$ 就會再繞回 $p$ ，所以等價於找下 $x \mod k$ 個。

可以使用 BIT + 二分搜來處理。前者維護是否還在圓圈內。

實作上，因為是圓環有點難處理，在找下一個的時候，可以以第 $n$ 個人切成兩段。

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
 
int n,k;
vector<int>bit;
 
int lowbit(int x){
    return(x & (-x));
}
 
void Update(int id,int val){
    for(;id<=n;id+=lowbit(id)) bit[id]+=val;
}
 
int query(int id){
    int res = 0;
    for(;id;id-=lowbit(id)) res += bit[id];
    return(res);
}
 
int get(int l,int r){
    if(r > n) return(get(l,n) + get(1,r-n));
    return(query(r) - query(l-1));
}
 
int BST(int nw,int t){
    int res=nw , l = 0, r = n , m;
    while(l<=r){
        m = (l+r)/2;
        
        int x = get(nw,nw+m);
 
        if(x >= t){
            int idx = (nw + m + n - 1) % n + 1;
            if(get(idx,idx)) res = idx;
            r = m-1;
        }else{
            l = m+1;
        }
    }
    return(res);
}
 
int main(){
    ios::sync_with_stdio(0),cin.tie(0);
 
    cin>>n>>k;
    k++;
 
    bit.resize(n+1);
 
    for(int i=0;i<n;i++) Update(i+1,1);
 
    int nw = 1;
    for(int i=0;i<n;i++){
        int t = k;
        t %= get(1,n);
        if(t==0) t = get(1,n);
 
        int idx = BST(nw , t);
        cout<<idx<<" ";
 
        Update(idx,-1);
 
        nw = idx;
    }
    return(0);
}
```

---
