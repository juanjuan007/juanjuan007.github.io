---
layout: post
title: "Week1"
tags: ["Sprout-2026"]
---

### 上機

---

#### 1. 大善人老闆救濟東南亞兒童
[SproutOJ 18](http://tioj.sprout.tw/problems/18/)

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

Time : $O(TN)$

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

#### 2. 中國人排隊問題
[SproutOJ 19](https://tioj.sprout.tw/problems/19/)

WIP

---

#### 3. 陸行鳥大賽車
[SproutOJ 20](https://tioj.sprout.tw/problems/20/)

<br>

**思路**

支援「刪除、「交換」元素，使用 Linked List。

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

#### 4. stack 練習
[SproutOJ 35](https://tioj.sprout.tw/problems/36)

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

#### 5. queue 練習
[SproutOJ 36](https://tioj.sprout.tw/problems/36)

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



