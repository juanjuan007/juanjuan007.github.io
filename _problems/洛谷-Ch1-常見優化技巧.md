---
layout: post
title: "CH1-常見優化技巧"
tags: ["洛谷"]
---

---

### A-B 數對
[Luogu P1102](https://www.luogu.com.cn/problem/P1102)

<br>

**思路**

<br>

&lt;M一&gt;

枚舉所有可能的 $i$ 值，再數有幾個 $a_{j} = C - a_{i}$，取總和即為答案。

上述計數過程可以使用 map 來輔助計數。

要特別注意 $A = C - A$ 的狀況，題敘說明 $i \ne j$，故要 $-1$。

Time : $O(n \log n)$
<br>

Code :

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long

signed main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int N,C;
    cin>>N>>C;
    vector<int>num(N);
    for(int i=0;i<N;i++) cin>>num[i];
    sort(num.begin(),num.end());
    int res = 0,a,b,l=0,r=0;
    while(l<=r && r<N){
        a = 1,b = 0;
        while(l+1<N && num[l+1]==num[l]){
            a++;
            l++;
        }
        while(r<N && num[r]<=num[l]+C){
            if(num[r]==num[l]+C) b++;
            r++;
        }
        res += a*b;
        l++;
    }
    cout<<res;
    return(0);
}
```

<br>

&lt;M二&gt;

將 $a$ 排序過後，使用上述做法會發現 : $i$ , $j$ 都是單調不降的。

所以使用雙指針，紀錄目前的配對。

因為陣列中的值可能重複，所以要一次數完所有相同的配對，避免漏算。

Time : $O(n \log n)$ ， 常數較 <M一> 小。

<br>

Code :

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int  long long

signed main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int N,C;
    cin>>N>>C;
    vector<int>num(N);
    for(int i=0;i<N;i++) cin>>num[i];
    sort(num.begin(),num.end());
    int res = 0,a,b,l=0,r=0;
    while(l<=r && r<N){
        a = 1,b = 0;
        while(l+1<N && num[l+1]==num[l]){
            a++;
            l++;
        }
        while(r<N && num[r]<=num[l]+C){
            if(num[r]==num[l]+C) b++;
            r++;
        }
        res += a*b;
        l++;
    }
    cout<<res;
    return(0);
}
```

<br>

---

### 逛畫展

[P1638](https://www.luogu.com.cn/problem/P1638)

<br>

**思路**

我們可以透過枚舉以第 $r$ 個為右端點的最短區間，就會發現 : 如果存在區間 $$[l,r]$$ 滿足，那麼當 $r$ 增加時，$l$ 會是單調不降的。

因為如果當前區間是合法的，那要盡可能的增大 $l$，使得我們現在找到的是最小的區間；
反之如果是不合法的，那代表以當前為右端點不存在區間滿足條件，這點易證。

Time : $O(n)$

<br>

Code :

```cpp
#include<bits/stdc++.h>
using namespace std;
#define p pair<int,int>
#define F first
#define S second

int main(){
    int n,m;
    cin>>n>>m;
    int nw = 0;
    vector<int>d(n),cnt(m);
    for(int i=0;i<n;i++) cin>>d[i];
    p ans = {-1,-1};
    int l=0,r=0;
    for(;r<n;r++){
        if(cnt[d[r]-1]==0) nw++;
        cnt[d[r]-1]++;
        if(nw < m) continue;
        while(cnt[d[l]-1] > 1){
            cnt[d[l]-1]--;
            l++;
        }

        if(ans.F==-1) ans = {l,r};
        
        if(r-l == ans.S-ans.F && l<ans.F) ans = {l,r};
        
        if(r-l < ans.S-ans.F) ans = {l,r};
    }

    cout<<ans.F+1<<" "<<ans.S+1<<'\n';
    return(0);
}
```

<br>

---

### 最大子段和

[P1115](https://www.luogu.com.cn/problem/P1115)

<br>

**思路**


令 $dp_{i}$ 代表以 $i$ 為結尾的子陣列最大和，

那麼會有 $dp_{i} = max(dp[i-1] + a_{i} , a_{i})$，也就是接在前一項後面或重新開始。從中取最大值就是答案。

注意到更新時只會用到前一項，所以前面的其實都不重要了，所以只要儲存前一項即可。

Time : $O(n)$

<br>

Code :

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
signed main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int n;
    cin>>n;
    int ans = -LLONG_MAX,nw=0,x;
    for(int i=0;i<n;i++){
        cin>>x;
        nw = max(nw + x , x);
        ans = max(nw , ans);
    }
    cout<<ans;
    return(0);
}
```

<br>

---

### 直播獲獎

[P7072](https://www.luogu.com.cn/problem/P7072)

<br>

**思路**

<br>

&lt;M一&gt;

令 $cnt_{i}$ 代表 $i$ 的出現次數。

查詢就等價於找到最大的 $i$ 滿足 $cnt_{i} + cnt_{i+1} + ... >= max( p*w% , 1)$，這點可以用二分搜。

上述可以使用 BIT 或線段樹來高效維護。

Time : $O(n log^{2} n)$

<br>

Code :
```cpp
#include<bits/stdc++.h>
using namespace std;

int sz;
vector<int>bit;

int lowbit(int x){
    return(x & (-x));
}

void update(int id,int val){
    for(;id<=sz;id+=lowbit(id)) bit[id] += val;
}

int query(int id){
    int res = 0;
    for(;id;id-=lowbit(id)) res += bit[id];
    return(res);
}

int main(){
    ios::sync_with_stdio(0) , cin.tie(0);
    
    int n,w;
    cin>>n>>w;
    vector<int>s(n) , r;

    for(int i=0;i<n;i++) cin>>s[i];
    
    r = s;
    sort(r.begin(),r.end());
    r.resize(unique(r.begin(),r.end()) - r.begin());
    for(int i=0;i<n;i++) s[i] = lower_bound(r.begin(),r.end(),s[i]) - r.begin() + 1;

    sz = r.size() + 1;
    bit.resize(sz+1);

    for(int i=0;i<n;i++){
        update(s[i] , 1);
        
        int cnt = (i+1) - max((i+1)*w/100 , 1) + 1;
        int res,L = 1,R = sz,M;
        while(L<=R){
            M = (L+R)/2;
            if(query(M) >= cnt){
                res = M;
                R = M-1;
            }else{
                L = M+1;
            }
        }
        cout<<r[res-1]<<" ";
    }
    return(0);
}
```

<br>

&lt;M二&gt;

易知獲獎人數必定是單調不降的，所以可以維護兩個 priority_queue，其中一個是會領到獎的，另一個則否。那維護前者的大小即可。

Time : $$O(n \log n)$$

<br>

Code :

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int n,w;
    cin>>n>>w;
    vector<int>num(n);
    priority_queue<int,vector<int>,greater<>>pq1;
    priority_queue<int>pq2;
    for(int i=0;i<n;i++){
        cin>>num[i];

        pq1.push(num[i]);
        pq2.push(pq1.top()); pq1.pop();

        int nw = max((i+1)*w/100,1);
    
        while(pq1.size() < nw){
            pq1.push(pq2.top());
            pq2.pop();
        }

        cout<<pq1.top()<<" ";
    }
    return(0);
}
```

<br>

&lt;M三&gt;

最直覺的想法是新加入一個數就排序一次，但這樣效率不彰，應使用其他種排序方式。

使用計數排序。查詢時只需要不斷累加直到獲獎人數。

Time : $O(An)$ ， $A$ : 成績值域

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

int main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int n,w,s,p;
    cin>>n>>w;

    vector<int>cnt(605,0);
    
    for(int i=0;i<n;i++){
        cin>>s;
        cnt[s]++;
        p = max((i+1)*w/100 , 1);
        for(int j=600;j>=0;j--){
            p -= cnt[j];
            if(p<=0){ cout<<j<<" "; break;}

        }
    }
    return(0);
}
```

<br>

---

### 求和

[Luogu P2671](https://www.luogu.com.cn/problem/P2671)

WIP

<br>

---

### 玉蟾宮
[Luogu P4147](https://www.luogu.com.cn/problem/P4147)

<br>

**思路**

枚舉每個 row 當矩形下側的狀況，此時矩形便會由一段連續的 column 構成。

令 $h_{i}$ 表示第 $i$ 個 column 往上連續可以延伸幾格，則當前 row 會形成類似柱狀圖。

注意到一個矩形的高度會由其中最矮的 column 決定，因此可以枚舉每個 column 作為矩形高度，並向左右尋找第一個高度 $< h_{i}$ 的位置，因為中間所有 column 的高度皆 $\geq h_{i}$ ，故這段區間皆可形成高度為 $h_{i}$ 的合法矩形。

而上述尋找最近較小值可以用 monotonic stack 來完成。

這種做法意如「懸一條線向左右掃去」，所以又稱為「懸線法」。

Time : $O(n^{2})$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int n,m; char ch;
    cin>>n>>m;
    vector<vector<int>>pre(n,vector<int>(m,-1));
    int ans = 0;
    for(int i=0;i<n;i++){
        vector<int>h(m),l(m),r(m);
        for(int j=0;j<m;j++){
            cin>>ch;
            if(ch=='R') pre[i][j] = i;
            else if(ch=='F' && i>0) pre[i][j] = pre[i-1][j];
            h[j] = i - pre[i][j];
        }
        stack<int>st;
        for(int j=0;j<m;j++){
            while(!st.empty() && h[st.top()] >= h[j]) st.pop();
            if(st.empty()) l[j] = -1;
            else l[j] = st.top();
            st.push(j);
        }
        while(!st.empty()) st.pop();
        for(int j=m-1;j>=0;j--){
            while(!st.empty() && h[st.top()] >= h[j]) st.pop();
            if(st.empty()) r[j] = m;
            else r[j] = st.top();
            st.push(j);
        }

        for(int j=0;j<m;j++){
            ans = max(ans,(r[j]-l[j]-1)*h[j]);
        }
    }
    cout<<ans*3;
    return(0);
}
```


<br>
---


### Bad Hair Day

[Luogu P2866](https://www.luogu.com.cn/problem/P2866)

<br>

**思路**

我們先把整條隊伍反轉過來，意即由 $1 ~ N$ 排序，以便接下來說明。

題敘條件等價於 : 第 $i$ 頭能看到第 $j$ 頭 ($i < j$) 的條件是 $max(h_{i} , h_{i+1} , ... , h_{j}) = h_{i}$，也就是可以看到 (右側第一頭較高左側) ， 那這點可以使用 monotonic stack 求得。

Time : $O(n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long 
signed main(){
    ios::sync_with_stdio(0) , cin.tie(0);
    int n,ans = 0;
    cin>>n;
    vector<int>h(n);
    for(int i=0;i<n;i++) cin>>h[i];
    stack<int>st;
    for(int i=n-1;i>=0;i--){
        while(!st.empty() && h[st.top()] < h[i]) st.pop();
        if(st.empty()) ans += n-i-1;
        else ans += st.top()-i-1;
        st.push(i);
    }
    cout<<ans;
    return(0);
}
```

<br>

---

### 長方形
[Luogu P1950](https://www.luogu.com.cn/problem/P1950)

<br>

**思路**

枚舉每個 row 當矩形下側的狀況，並枚舉其中每個 column 的高度向左右延伸有幾種剪法。

若當前在 column $i$ ，向上高度 $h_{i}$ 向左、右分別可延伸至 $l_{i} , r_{i}$，則剪法有 $(i - l_{i} + 1) \times (r_{i} - i - 1) \times h_{i}$ 。

取總和即是答案。

Time : $O(n \log n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long 
signed main(){
    ios::sync_with_stdio(0) , cin.tie(0);

    int n,m;
    cin>>n>>m;

    vector<vector<bool>>g(n,vector<bool>(m));
    
    char ch;
    for(int i=0;i<n;i++){
        for(int j=0;j<m;j++){
            cin>>ch;
            if(ch=='.') g[i][j] = 1;
            else if(ch=='*') g[i][j] = 0;
        }
    }

    int ans = 0;
    vector<int>h(m) , l(m) , r(m);
    for(int i=0;i<n;i++){
        for(int j=0;j<m;j++)
            if(g[i][j]) h[j]++;
            else h[j] = 0;
        
        stack<int>st;
        
        for(int j=0;j<m;j++){
            while(!st.empty() && h[j] < h[st.top()]) st.pop();
            if(st.empty()) l[j] = -1;
            else l[j] = st.top();
            st.push(j);
        }
        
        while(!st.empty()) st.pop();
        
        for(int j=m-1;j>=0;j--){
            while(!st.empty() && h[j] <= h[st.top()]) st.pop();
            if(st.empty()) r[j] = m;
            else r[j] = st.top();
            st.push(j);
        }
        
        for(int j=0;j<m;j++)
            ans += (j-l[j])*(r[j]-j)*h[j];
    }
    cout<<ans;
    return(0);
}
```

<br>

---

### 掃描
[Luogu P2032](https://www.luogu.com.cn/problem/P2032)

<br>

**思路**

可以發現 : 此時木板最右側是第 $i$ 個數字，若 $j < i$ 滿足 $a_{j} \leq a_{i}$ ，則第 $j$ 個數字不可能是接下來的答案之一。

因此，使用 monotonic deque ， 在 deque 中維護可能成為最大值的數字並確保其中都還在木板覆蓋範圍內。

Time : $O(n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int n,k;
    cin>>n>>k;
    vector<int>num(n);
    deque<int>dq;
    for(int i=0;i<n;i++){
        cin>>num[i];
        while(!dq.empty() && num[dq.back()] <= num[i]) dq.pop_back();
        while(!dq.empty() && dq.front()<=i-k) dq.pop_front();
        dq.push_back(i);
        if(i+1>=k) cout<<num[dq.front()]<<'\n';
    }
    return(0);
}
```

<br>

---

### 理想的正方形
[Luogu P2216](https://www.luogu.com.cn/problem/P2216)

<br>

**思路**

使用 monotonic deque。

對每個 row 分別求出當前子陣列最小、大值，再求出其子陣列最小、大值。

也可以解釋成在兩個維度維護單調性。

Time : $O(n^{2})$

<br>
    
Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    int a,b,n,ans = INT_MAX;
    cin>>a>>b>>n;
    vector<vector<int>>num(a,vector<int>(b));
    for(int i=0;i<a;i++)
        for(int j=0;j<b;j++)
            cin>>num[i][j];
    vector<deque<int>>mini(a),maxi(a);
    for(int j=0;j<b;j++){
        deque<int>mnq,mxq; 
        for(int i=0;i<a;i++){
            while(!mini[i].empty() && num[i][mini[i].back()]>=num[i][j]) mini[i].pop_back();
            while(!mini[i].empty() && mini[i].front() <= j-n) mini[i].pop_front();
            mini[i].push_back(j);
            
            while(!mnq.empty() && num[mnq.back()][mini[mnq.back()].front()] >= num[i][mini[i].front()]) mnq.pop_back();
            while(!mnq.empty() && mnq.front()<=i-n) mnq.pop_front();
            mnq.push_back(i);
            

            while(!maxi[i].empty() && num[i][maxi[i].back()]<=num[i][j]) maxi[i].pop_back();
            while(!maxi[i].empty() && maxi[i].front() <= j-n) maxi[i].pop_front();
            maxi[i].push_back(j);

            while(!mxq.empty() && num[mxq.back()][maxi[mxq.back()].front()] <= num[i][maxi[i].front()]) mxq.pop_back();
            while(!mxq.empty() && mxq.front()<=i-n) mxq.pop_front();
            mxq.push_back(i); 

            if(i+1>=n && j+1>=n){
                ans = min(ans,num[mxq.front()][maxi[mxq.front()].front()] - num[mnq.front()][mini[mnq.front()].front()]);
            }
        }
    }
    cout<<ans;
    return(0);
}
```

<br>
---


### 唯一的雪花
[UVa 11572](https://vjudge.net/problem/uva-11572)

<br>

**思路**

枚舉以第 $r$ 個為右端點的最大區間，易知 $l$ 會是單調不降的。

也就是若 $a_{r}$ 在當前區間已經出現過，則要將左界右移，而是否出現過可以用 map 做判斷。

Time : $O(n \log n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;


void solve(){
    int n;
    cin>>n;
    vector<int>a(n);
    for(int i=0;i<n;i++) cin>>a[i];

    map<int,bool>mp;
    
    int ans=0,l=0,r=0;
    while(r < n){
        while(mp[a[r]]){
            mp[a[l]] = false;
            l++;
        }
        mp[a[r]] = true;
        ans = max(r-l+1 , ans);
        r++;
    }
    cout<<ans<<'\n';
    return;
}

int main(){
    int t;
    cin>>t;
    while(t--) solve();
    return(0);
}
```


<br>
---

###  Sure Bet

[Luogu P4653](https://www.luogu.com.cn/problem/P4653)

<br>

WIP

<br>
---

###  Diamond Collector

[Luogu P3143](https://www.luogu.com.cn/problem/P3143)

<br>

**思路**

對於放入展示櫃的能放就要盡可能的放，因為要放入最多個。

但是目前無法確定對於每個鑽石要選擇哪些，因此不妨令 $x_{i}$ 代表當第 $i$ 顆鑽石是整個展覽櫃最大者有幾顆鑽石。

而共有兩個展覽櫃可放，當第 $i$ 顆是其中一個展覽櫃最大者時，不失一般性假設另一個展覽櫃都放較小的鑽石，那就是所有 $j$ 滿足 $w_{j} < w_{i} - k$ 中， $x_{j}$ 最大者。

為了更簡易維護上述事情，先將 $w$ 排序，就可以使用二分搜之類來作。

Time : $O(n \log n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

int main(){
    ios::sync_with_stdio(0),cin.tie(0);

    int n,k;
    cin>>n>>k;
    vector<int>w(n) , x(n);
    for(int i=0;i<n;i++) cin>>w[i];
    sort(w.begin(),w.end());

    for(int i=0;i<n;i++){
        auto it = lower_bound(w.begin(),w.end(),w[i]-k) - w.begin();
        x[i] = i - it + 1;
    }

    int ans = 0,nw = 0 , pre=0;
    for(int i=0;i<n;i++){
        while(w[nw] < w[i]-k){ pre = max(x[nw] , pre); nw++; }
        ans = max(x[i] + pre , ans);
    }
    cout<<ans;
    return(0);
}
```

<br>

---

### 插入排序
[Luogu P7910](https://www.luogu.com.cn/problem/P7910)

<br>

&lt;M一&gt;

暴力做。
對於操作二直接計數，要特別注意是穩定排序，也就是若 $j > i$ 且 $a_{j} = a_{i}$ ，則排序後 $j$ 會在 $i$ 後方。

Time : $O(nq)$

<br>

Code :
```cpp
#include<bits/stdc++.h>
using namespace std;

int main(){
    ios::sync_with_stdio(0),cin.tie(0);

    int n,q;
    cin>>n>>q;
    vector<int>a(n);
    for(int i=0;i<n;i++) cin>>a[i];
    int t,x,v,res;
    while(q--){
        cin>>t;
        if(t==1){
            cin>>x>>v;
            x--;
            a[x] = v;
        }else if(t==2){
            cin>>x;
            x--;
            res = 0;
            for(int i=0;i<x;i++) res += (a[i] <= a[x]);
            for(int i=x+1;i<n;i++) res += (a[i] < a[x]);
            cout<<res+1<<'\n';  
        }
    }
    return(0);
}
```

<br>

&lt;M二&gt;

類似計數排序。

紀錄每個數字的出現次數並支援求區間和，以及在它前方相同的數字有幾個，才能判斷是在哪個位置。

前者需要離線處理、離散化後，使用 BIT 或線段樹處理，~~也可以寫動態開點線段樹~~。

後者則是直接暴力維護即可，因為保證操作一的次數至多 $5000$。

Time : $O(n \log n + mn + (q-m) \log n)$，其中 $m$ 為操作一次數。

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

int sz;
vector<int>bit;

int lowbit(int x){
    return(x & (-x));
}

void Update(int id,int val){
    for(;id<=sz;id+=lowbit(id)) bit[id]+=val;
}

int query(int id){
    int res = 0;
    for(;id;id-=lowbit(id)) res += bit[id];
    return(res);
}

int main(){
    ios::sync_with_stdio(0),cin.tie(0);
    
    int n,Q;
    cin>>n>>Q;
    
    vector<int>num(n),r;
    for(int i=0;i<n;i++) cin>>num[i];
    r = num;
    
    vector<array<int,3>>qry(Q);
    for(int i=0;i<Q;i++){
        cin>>qry[i][0]>>qry[i][1];
        qry[i][1]--;
        if(qry[i][0]==1){
            cin>>qry[i][2];
            r.push_back(qry[i][2]);
        }
    }

    sort(r.begin(),r.end());
    r.resize(unique(r.begin(),r.end())-r.begin());
    sz = r.size();
    
    bit.resize(sz+1);
    vector<int>cnt(sz+1),p(n);
    for(int i=0;i<n;i++){
        num[i] = lower_bound(r.begin(),r.end(),num[i])-r.begin()+1;
        p[i] = cnt[num[i]]++;
        Update(num[i],1);
    }
    
    for(int i=0;i<Q;i++){
        if(qry[i][0]==1)
            qry[i][2] = lower_bound(r.begin(),r.end(),qry[i][2]) - r.begin() + 1;
    }

    for(int i=0;i<Q;i++){
        if(qry[i][0]==1){
            cnt[num[qry[i][1]]]--;
            cnt[qry[i][2]]++;
            
            int nw = 0;
            for(int j=0;j<qry[i][1];j++){
                if(num[j]==qry[i][2]) nw++;
            }
            p[qry[i][1]] = nw++;
            
            for(int j=qry[i][1]+1;j<n;j++){
                if(num[j]==num[qry[i][1]]){
                    p[j]--;
                }
                if(num[j]==qry[i][2]){
                    p[j]++;
                }
            }
            Update(num[qry[i][1]],-1);
            Update(qry[i][2],1);
            num[qry[i][1]] = qry[i][2];
        }else if(qry[i][0]==2){
            int res = query(num[qry[i][1]]);
            if(cnt[num[qry[i][1]]] > 1){
                res -= (cnt[num[qry[i][1]]] - 1 - p[qry[i][1]]);
            }
            cout<<res<<'\n';
        }
    }
    return(0);
}
```

<br>
---

### 奶牛浴場

[Luogu P1578](https://www.luogu.com.cn/problem/P1578)

WIP


<br>
---

### PLA -Postering

[Luogu P3467](https://www.luogu.com.cn/problem/P3467)

<br>

**思路**

建築物的寬度明顯不會影響到海報的個數只會影響其寬度，故在下述做法中不探討寬度。

<br>

&lt;M一&gt;

以範測(左)為例，我的切法(右) 會類似於下圖，

<img width="188" height="246" alt="螢幕擷取畫面 2026-05-24 095701" src="https://github.com/user-attachments/assets/333caeb1-89ec-482b-9b1e-55ccdfb6800f" />
<img width="188" height="246" alt="螢幕擷取畫面 2026-05-24 102505" src="https://github.com/user-attachments/assets/35550423-7998-4995-921d-d6882fe1940c" />

我們可以來體會這個做法的正確性，

觀察到每棟建築物的上方會貼齊海報的上沿覆蓋，又此張海報至多能延伸至兩側最近較矮的建築。

可以利用 monotonic stack 對每棟建築都計算上述的事情，

若 ${l_{i} , r_{i}}$ 相同，則代表可以用一張海報即可，這點易證；

否則對於每個區間 ${l,r}$ 都需要一張海報。

Time : $O(n \log n)$

<br>

Code : 
```cpp
#include <bits/stdc++.h>
using namespace std;

#define p pair<int,int>
#define F first
#define S second 

int main() {
    ios:sync_with_stdio(0),cin.tie(0);

    int n,d,w;
    cin>>n;
    
    vector<int>h(n);
    for(int i=0;i<n;i++){
        cin>>d>>w;
        h[i] = w;
    }
    vector<p>arr(n);
    stack<int>st;
    for(int i=0;i<n;i++){
        while(!st.empty() && h[st.top()]>=h[i]) st.pop();
        if(st.empty()) arr[i].F = 0;
        else arr[i].F = st.top()+1;
        st.push(i);
    }
    
    while(!st.empty()) st.pop();
    
    for(int i=n-1;i>=0;i--){
        while(!st.empty() && h[st.top()]>=h[i]) st.pop();
        if(st.empty()) arr[i].S = n-1;
        else arr[i].S = st.top()-1;
        st.push(i);
    }
    
    set<p>s;
    for(int i=0;i<n;i++) s.insert(arr[i]);
    cout<<s.size();
    
    return(0);
}
```

&lt;M二&gt;

新加入一棟建築，會分成以下幾種情況，

case 1 : 有一棟建築物與其等高且兩者之間沒有更矮的建築

不需要增加海報，可以直接從那棟建築的海報延伸過來。

case 2 : else

增加一張海報。

判別條件可以直接用 monotonic stack 維護。

Time : $O(n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

int main(){
    ios::sync_with_stdio(0) , cin.tie(0);
    int n;
    cin>>n;

    int h,w;
    
    int ans = 0;
    stack<int>st;

    for(int i=0;i<n;i++){
        cin>>h>>w;
        while(!st.empty() && st.top() > w) st.pop();
        ans++;
        if(!st.empty() && st.top()==w) ans--;
        st.push(w);
    }
    cout<<ans;
    return(0);
}
```

<br>
---


### 單調隊列、滑動窗口

[Luogu P1886](https://www.luogu.com.cn/problem/P1886)

<br>

**思路**

最小值、最大值做法差異不大，在此論只討論最小值的情況。

若窗口最右側是第 $i$ 個數字，若 $j < i$ 滿足 $a_{j} \leq a_{i}$ ，則第 $j$ 個數字不可能是接下來的答案之一。

因此，使用 monotonic deque ， 在 deque 中維護可能成為最大值的數字並確保其中都還在窗口覆蓋範圍內。

Time : $O(n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long

signed main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int n,k;
    cin>>n>>k;
    vector<int>num(n);
    deque<int>dq1,dq2;
    vector<int>ans1,ans2;
    for(int i=0;i<n;i++){
        cin>>num[i];
        while(!dq1.empty() && num[dq1.back()]<=num[i]) dq1.pop_back();
        while(!dq1.empty() && dq1.front() <= i-k) dq1.pop_front();
        dq1.push_back(i);
        if(i+1>=k) ans1.push_back(num[dq1.front()]);
    
        while(!dq2.empty() && num[dq2.back()]>=num[i]) dq2.pop_back();
        while(!dq2.empty() && dq2.front() <= i-k) dq2.pop_front();
        dq2.push_back(i);
        if(i+1>=k) ans2.push_back(num[dq2.front()]);
    }
    for(int i:ans2) cout<<i<<" ";
    cout<<'\n';
    for(int i:ans1) cout<<i<<" ";
    return(0);
}
```

<br>

---

### Balanced Lineup

[Luogu P2880](https://www.luogu.com.cn/problem/P2880)

<br>

**思路**

需要一個能支援區間差尋極值得資料結構，可以使用 Sparse Table 或 線段樹。

Time : $O (q + n) \log n$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

struct Node{
    int mini,maxi;  
};

int n;
vector<int>num;
vector<Node>seg;

void pull(int id){
    seg[id].mini = min(seg[id*2+1].mini,seg[id*2+2].mini);
    seg[id].maxi = max(seg[id*2+1].maxi,seg[id*2+2].maxi);
}

void Build(int l,int r,int id){
    if(l==r){
        seg[id].mini = num[l];
        seg[id].maxi = num[l];
        return;
    }
    int m = (l+r)/2;
    Build(l,m,id*2+1);
    Build(m+1,r,id*2+2);
    pull(id);
}

int query(int ql,int qr,int type,int l,int r,int id){
    if(qr < l || r < ql){
        if(type==0) return(INT_MAX);
        else if(type==1) return(-1);
    }
    
    if(ql<=l && r<=qr){
        if(type==0) return(seg[id].mini);
        else if(type==1) return(seg[id].maxi);
    }
    
    int m = (l+r)/2;
    
    if(type==0) return(min(query(ql,qr,type,l,m,id*2+1) , query(ql,qr,type,m+1,r,id*2+2)));
    else if(type==1) return(max(query(ql,qr,type,l,m,id*2+1) , query(ql,qr,type,m+1,r,id*2+2)));
}

int main(){
    int q;
    cin>>n>>q;
    num.resize(n);
    for(int i=0;i<n;i++) cin>>num[i];
    seg.resize(4*n);
    Build(0,n-1,0);
    int a,b;
    for(int i=0;i<q;i++){
        cin>>a>>b;
        a--; b--;
        cout<<query(a,b,1,0,n-1,0) - query(a,b,0,0,n-1,0)<<'\n';
    }
    return(0);    
}
```

<br>
---

### 切蛋糕
[Luogu P1714](https://www.luogu.com.cn/problem/P1714)

**思路**

求一段連續區間不妨使用前綴和，查詢區間 $[l,r]$ 即是 $p_{r} - p_{l-1}$。

區間長度至多是 $m$，也就是當 $i$ 是整段區間的末端時，起點 $j$ 必須滿足 $i-m \leq j \leq i$。

根據上述思路，我們可以枚舉每塊蛋糕做為終點，在 deque 中維護目前可能的值，並維護其單調性。

Time : $O(n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long 

signed main(){
    int n,m;
    cin>>n>>m;
    vector<int>arr(n),p(n+1);
    for(int i=0;i<n;i++){
        cin>>arr[i];
        p[i+1] = p[i] + arr[i];
    }
    int ans = -LLON_MAX;
    deque<int>dq;
    dq.push_back(0);
    for(int i=1;i<=n;i++){
        while(!dq.empty() && dq.front() < i-m) dq.pop_front();
        ans = max(p[i]-p[dq.front()],ans);    
        while(!dq.empty() && p[dq.back()]>=p[i]) dq.pop_back();
        dq.push_back(i);
    }

    cout<<ans;
    return(0);
}
```

<br>
---

### 琪露諾
[Luogu P1725](https://www.luogu.com.cn/problem/P1725)

<br>

**思路**

" 在格子 $i$ 能移動到 $[i+L,i+R]$ "，可以轉換成 "位置 $i$ 可由 $[i-R , i-L]$ 移動一步來。

令 $dp_{i}$ 表走到 $i$ 可能獲得的最大冰凍指數，很顯然就會有 $dp_{i} = min(dp_{j}) + A_{i} , i-R\leq j \i-L$  。

更新方式可以使用 monotonic deque 維護。

注意題敘說明只要下一步走到的位置編號 $> N$ 即到對岸，所以答案是 $max(dp_{n - R + 1} , ... , dp_{n-1})$ 。

Time : $O(n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long 

signed main(){
    ios::sync_with_stdio(0),cin.tie(0);

    int n,L,R;
    cin>>n>>L>>R;
    vector<int>num(n+1),dp(n+1,-1e9),use(n+1,0);
    for(int i=0;i<=n;i++) cin>>num[i];
    
    dp[0] = 0;

    deque<int>dq;
    for(int i=0;i<=n;i++){
        int l = max(i-R,0LL) ,r = min(i-L,n);
        if(r < 0) continue;
        
        while(!dq.empty() && dp[dq.back()] <= dp[r]) dq.pop_back();
        while(!dq.empty() && dq.front() < l) dq.pop_front();
        if(dp[r] != -1e18) dq.push_back(r);

        dp[i] = num[i] + dp[dq.front()];
    }

    int ans = -1e9;
    for(int i=n-R+1;i<=n;i++) ans = max(dp[i],ans);
    cout<<ans;
    return(0);
}
```

---
