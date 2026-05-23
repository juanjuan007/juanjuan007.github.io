---
layout: post
title: "洛谷-CH1-常見優化技巧"
tags: ["洛谷" , "-1"]
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

### 求和

[Luogu P2671](https://www.luogu.com.cn/problem/P2671)

WIP

### 玉蟾宮
[Luogu P4147](https://www.luogu.com.cn/problem/P4147)

<br>

**思路**

枚舉每個 row 當矩形下側的狀況，此時矩形便會由一段連續的 column 構成。

令 $h_{i}$ 表示第 $i$ 個 column 往上連續可以延伸幾格，則當前 row 會形成類似柱狀圖。

注意到一個矩形的高度會由其中最矮的 column 決定，因此可以枚舉每個 column 作為矩形高度，並向左右尋找第一個高度 $< h_{i}$ 的位置，因為中間所有 column 的高度皆 $\leq h_{i}$ ，故這段區間皆可形成高度為 $h_{i}$ 的合法矩形，。

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
