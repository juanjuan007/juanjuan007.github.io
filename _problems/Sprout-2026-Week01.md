---
layout: post
title: "Week01"
tags: ["Sprout-2026","list"]
---

---

### pA 大善人老闆救濟東南亞兒童
[Sprout 18.](http://tioj.sprout.tw/problems/18/)

<br>

**思路**

我們必須按照車廂在 B 車站的進入順序依次進站，那我們考慮現在該進站的是 車廂 $x$ ，會分成以下幾種 case : 

- 在車站 A
  可以按照在車站 A 的順序依次移到中間車站，形成下面那個狀況。
- 在中間車站
  - 在最上面 : 移到車站 A
  - 非最上面 : 無解，因為在車廂 $x$ 上面的一定會比其先到車站 B
- 在車站 B
  不可能，因為我們必須按照順序

依照這樣的策略，可以直接做模擬。

Time : $O(N)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

void slove(){
    int n;
    cin>>n;
    vector<int>p(n);
    for(int i=0;i<n;i++) cin>>p[i];
    int a=1,b=0;
    stack<int>st;
    for(;a<=n;a++){
        st.push(a);
        while(!st.empty() && st.top()==p[b]){
            st.pop();
            b++;
        }
    }
    if(b==n) cout<<"Yes\n";
    else cout<<"No\n";
    return;
}

int main(){
    int t;
    cin>>t;
    while(t--) slove();
}
```

---

### pB 中國人排隊問題
[Sprout 19.](https://tioj.sprout.tw/problems/19/)

WIP

---

### pC 陸行鳥大賽車
[Sprout 20.](https://tioj.sprout.tw/problems/20/)

<br>

**思路**

支援「刪除」、「交換」元素，使用 Linked List。

Time : $O(n + m)$

<br>

Code :
```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long 
signed main(){
    int n,m,t,x,id;
    cin>>n>>m;
    
    vector<int>idx(n+1),ord(n),prv(n),nxt(n);
    iota(idx.begin(),idx.end(),-1);
    iota(ord.begin(),ord.end(),1);
    iota(prv.begin(),prv.end(),-1);
    iota(nxt.begin(),nxt.end(),1); nxt[n-1] = -1;
    
    for(int i=0;i<m;i++){
        cin>>t>>x;
        id = idx[x];
        if(t==0){
            if(prv[id]!=-1) nxt[prv[id]] = nxt[id];
            if(nxt[id]!=-1) prv[nxt[id]] = prv[id];
        }else if(t==1){
            if(prv[id]==-1) continue;
            idx[x] = prv[id];
            idx[ord[prv[id]]] = id;
            swap(ord[id],ord[prv[id]]);
        }
    }
    int h = 0;
    while(prv[h]==-1) h++;
    h--;
    while(h!=-1){
        cout<<ord[h]<<" ";
        h = nxt[h];
    }
    return(0);
}
```

---

### pD stack 練習
[Sprout 35.](https://tioj.sprout.tw/problems/36)

Time : $O(N)$

Code : 

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    stack<int>st;
    int t,n,x;
    cin>>t;
    while(t--){
        cin>>n;
        if(n==1){
            cin>>x; st.push(x);
        }else if(n==2){
            if(st.empty()){
                cout<<"empty!";
            }else{
                cout<<st.top();
                st.pop();
            }
            cout<<'\n';
        }
    }
    return(0);
}
```

---

### pE queue 練習
[Sprout 36](https://tioj.sprout.tw/problems/36)

Time : $O(N)$

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    queue<int>que;
    int t,n,x;
    cin>>t;
    while(t--){
        cin>>n;
        if(n==1){
            cin>>x; que.push(x);
        }else if(n==2){
            if(que.empty()){
                cout<<"empty!";
            }else{
                cout<<que.front();
                que.pop();
            }
            cout<<'\n';
        }
    }
    return(0);
}
```

---

### pF 檸檬汽水傳說
[Sprout 21.](https://tioj.sprout.tw/problems/21)

<br>

**思路**

依題目所述，若 $(i,j)$ 能溝通，代表不存在 $i < k < j$ 滿足 $A_{k} > min(A_{i} , A_{j})$，

也就是 $A_{i} \geq A_{k}$ 且 $A_{j} \geq A_{k}$，

換而言之，如果已經存在 $i < k$，滿足 $A_{i} < A_{k}$ 時，就不存在 $j > k$ 能讓　$(i,j)$ 溝通。

依序處理每個人是右側的狀況，可以用 stack 維護目前前面還有可能的人，但這樣若有相同者則無法計算。

有兩種方法。

<br>

&lt;M一&gt;

不移除相同的，

因為若有相同的數字代表是連續的，所以可以直接在 stack 上二分搜找連續相同的數量，


Time : $O(N \log N)$

<br>
Code : 
```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long
void slove(){
    int n,ans = 0;
    cin>>n;
    vector<int>lev(n);
    vector<int>st(n);
    int head=0,tail = 0;
    for(int i=0;i<n;i++){
        cin>>lev[i];
        while(tail > 0 && lev[i] > st[tail-1]){ tail--; ans++; }

        int l = head,r = tail-1,m,nw = tail;
        while(l <= r){
            m = (l+r)/2;
            if(st[m]==lev[i]){
                nw = m;
                r = m-1;
            }else{
                l = m+1; 
            }
        }        
        ans += tail - nw;
        if(nw > head) ans++;

        st[tail++] = lev[i];

    }
    cout<<ans<<'\n';
}

signed main(){
	int t;
	cin>>t;
	while(t--)
        slove();
  return(0);
}
```

&lt;M二&gt;

移除相同的。

必須對每個位置多紀錄 : 在入棧時有多少個相同值被其移除，就可以在之後的計算也加上這個值。

Time : $O(N)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long
#define p pair<int,int>
#define F first
#define S second

void solve(){
    int n;
    cin>>n;

    stack<p> st;
    int ans = 0;

    for(int i=0;i<n;i++){
        int x;
        cin>>x;
        int cnt = 1;
        while(!st.empty() && st.top().F <= x){
            ans += st.top().S;
            if(st.top().F == x)
                cnt += st.top().S;
            st.pop();
        }
        if(!st.empty()) ans++;
        st.push({x,cnt});
    }

    cout<<ans<<'\n';
}

signed main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int t;
    cin>>t;
    while(t--)
        solve();
   return(0);
}
```

---

### pG. 更不能輸給暴風雨

[Sprout 22.](https://tioj.sprout.tw/problems/22/)

<br>

**思路**

本質上非常類似上面那題。

我們必須按照車廂在 B 車站的進入順序依次進站，那我們考慮現在該進站的是 車廂 $x$ ，會分成以下幾種 case : 

- 在車站 A
  可以按照在車站 A 的順序依次移到右側車站，形成下面那個狀況。
- 在右側車站
  - 在最上面 : 移到左側車站。
  - 非最上面 : 按照在車站內的順序依次移到左側車站，形成上面那個狀況。
- 在左側車站
  - 在最上面 : 移到車站 B。
  - 非最上面 : 無解，因為上方的車廂無法移動。
- 在車站 B
  不可能，因為我們必須按照順序。

依照這樣的策略，可以直接做模擬。

Time : $O(N)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
#include "lib0022.h"
using namespace std;

void solve(int N,int order[]){
    int front = 1;
    stack<int>st1,st2;
    vector<int>nw(N+1,1);

    for(int i=0;i<N;i++){
        if(nw[order[i]]==1){
            while(front!=order[i]){
                push_train();
                nw[front] = 2;
                st1.push(front++);
            }
            front++;
            nw[order[i]] = 4;
            push_train();
            move_station_1_to_2();
            pop_train();
        }else if(nw[order[i]]==2){
            while(st1.top()!=order[i]){
                st2.push(st1.top());
                nw[st1.top()] = 3;
                st1.pop();
                move_station_1_to_2();
            }
            nw[order[i]] = 4;
            st1.pop();
            move_station_1_to_2();
            pop_train();
        }else if(nw[order[i]]==3){
            while(st2.top()!=order[i]){
                st1.push(st2.top());
                nw[st2.top()] = 2;
                st2.pop();
                move_station_2_to_1();
            }
            nw[order[i]] = 4;
            st2.pop();
            pop_train();
        }else{
            no_solution();
        }
    }
    return;
}
```

---

### pH. 一天遊戲只能一小時

[Sprout 24.](https://tioj.sprout.tw/problems/24/)

<br>

**思路**

直接用 queue 模擬。

Time : $O(M)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

int main(){
    ios::sync_with_stdio(0),cin.tie(0);

    int N,M;
    cin>>N>>M;

    vector<list<int>> arr(N+1);

    string s;
    int x,y;

    while(M--){
        cin>>s;

        if(s=="ADD"){
            cin>>x>>y;
            arr[x].push_back(y);
        }
        else if(s=="LEAVE"){
            cin>>x;
            if(arr[x].empty())
                cout<<"queue "<<x<<" is empty!\n";
            else
                arr[x].pop_front();
        }
        else{
            cin>>x>>y;
            arr[y].splice(arr[y].end(), arr[x]);
        }
    }

    for(int i=1;i<=N;i++){
        cout<<"queue "<<i<<":";
        if(arr[i].empty()) cout<<" empty";
        for(int v:arr[i]) cout<<" "<<v;
        if(i<N) cout<<"\n";
    }
    return(0);
}
```

---

### pI 霖澤黑森林

[Sprout 424.](https://tioj.sprout.tw/problems/424/)

<br>

**思路**

試管的本質是 stack，直接模擬即可。

Time : $O(N)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
signed main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int n,x;
    cin>>n;
    stack<int>st;
    for(int i=0;i<n;i++){
        cin>>x;
        if(x < 0){
            if(st.empty() || st.top()!=-x){
                cout<<"lose light light";
                return(0);
            }else{
                st.pop();
            }
        }else{
            st.push(x);
        }
    }

    if(st.empty()) cout<<"weed";
    else cout<<"lose light light";
    return(0);
}
```

---

### pJ 超大螢幕設置

[Sprout 425.](https://tioj.sprout.tw/problems/425/)

<br>

**思路**

枚舉每棟建築矩形上框時的狀況，左、右邊至多能到兩側最近較矮建築。

上述的事情，可以用 monotonic stack 來計算。

Time : $O(N)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
signed main(){
    int n;
    cin>>n;
    vector<int>h(n);
    for(int i=0;i<n;i++) cin>>h[i];
    vector<int>l(n);
    stack<int>st;
    for(int i=0;i<n;i++){
        while(!st.empty() && h[st.top()]>=h[i]) st.pop();
        if(st.empty()) l[i] = 0;
        else l[i] = st.top()+1;
        st.push(i);
    }
    while(!st.empty()) st.pop();
    vector<int>r(n);
    for(int i=n-1;i>=0;i--){
        while(!st.empty() && h[st.top()]>=h[i]) st.pop();
        if(st.empty()) r[i] = n+1;
        else r[i] = st.top()+1;
        st.push(i);
    }
    int ans = 0;
    for(int i=0;i<n;i++){
        ans = max((r[i]-l[i]-1)*h[i],ans);
    }
    cout<<ans;
    return(0);
}
```
---
