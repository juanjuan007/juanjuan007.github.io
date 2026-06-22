---
layout: post
title: "Week3"
tags: ["Sprout-2026" , "list"]
---

---
h

### pA 二元搜尋樹
[Sprout 47.](http://tioj.sprout.tw/problems/47/)

<br>

**思路**

前序是按照 : 根節點、左子節點、右子節點，後序則是 : 左子節點、右子節點、根節點。

依序遞迴處理子樹，故若是同一棵子樹則在二者中必定連續。

可以發現 : 在前序中，若區間 $[a,b]$ 代表一棵子樹，那首項就是根節點。

搭配上二元搜尋樹的性質，就可以切成兩邊，分成左、右子樹，再遞迴處理即可。

Time : $O(N \log N)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

int n;
vector<int>arr;

void f(int a,int b){
    if(a > b) return;
    int it=b+1,l=a+1,r=b,m;
    while(l<=r){
        m = (l+r)/2;
        if(arr[m] > arr[a]){
            it = m;
            r = m-1;
        }else{
            l = m+1;
        }
    }
    f(a+1,it-1);
    f(it,b);
    cout<<arr[a]<<'\n';
}

int main(){
    cin>>n;
    arr.resize(n);
    for(int i=0;i<n;i++) cin>>arr[i];
    f(0,n-1);
    return(0);
}
```

---

### pB 包禮物

[Sprout 48.](http://tioj.sprout.tw/problems/48/)

<br>

**思路**

如果在 $a$ 放入 $b$ 時，直接做加法，那會讓後來放入 $a$ 的不會被計算到。

所以應該要在所有的箱子都放完後再數，

上述情況就是建 $b \rightarow a$，那題目等價於求子樹大小。

可以從每棵樹的根節點用 BFS/DFS 處理。

Time : $O(TN)$

<br>

Code : 

```cpp
#include<bits/stdc++.h>
using namespace std;

int n,m,q;
vector<vector<int>>g;
vector<int>indeg , sz;

int dfs(int v){
    for(int u : g[v]){
        sz[v] += dfs(u);
    }
    return(sz[v]);
}

void solve(){
    cin>>n>>m;
    g.assign(n , {});
    indeg.assign(n,0);
    sz.assign(n,1);

    int x,y;
    for(int i=0;i<m;i++){
        cin>>x>>y;
        x--; y--;
        g[x].push_back(y);
        indeg[y]++;
    }

    for(int i=0;i<n;i++){
        if(indeg[i]==0){
            dfs(i);
        }
    }

    cin>>q; 

    while(q--){
        cin>>x;
        x--;
        cout<<sz[x]<<'\n';
    }
}

int main(){
    int t;
    cin>>t;
    while(t--) solve();
    return(0);
}
```

---

### pC 庭院裡的水池

[Sprout 41.](http://tioj.sprout.tw/problems/41/)

<br>

**思路**

把水池視為節點，相鄰的水池視為有邊，題目所求即為連通塊數。

可以寫 BFS/DFS/DSU 之類的。

Time : $O(THW)$

<br>

Code :
```cpp
#include<bits/stdc++.h>
using namespace std;
#define p pair<int,int>
#define F first
#define S second

vector<p>movef = {{0,-1},{-1,0},{0,1},{1,0}};

void slove(){
    int h,w;
    cin>>h>>w;
    vector<vector<bool>>g(h,vector<bool>(w,false));

    char ch;
    for(int i=0;i<h;i++){
        for(int j=0;j<w;j++){
            cin>>ch;
            if(ch=='.') g[i][j] = true;
        }
    }
    int res = 0;
    for(int i=0;i<h;i++){
        for(int j=0;j<w;j++){
            if(!g[i][j]) continue;
            g[i][j] = false;
            res++;
            queue<p>que;
            que.push({i,j});
            while(!que.empty()){
                auto [y,x] = que.front(); que.pop();
                for(auto [dy,dx] : movef){
                    int ny = y+dy,nx = x+dx;
                    if(!(0<=ny && ny<h && 0<= nx && nx<w)) continue;
                    if(g[ny][nx]) que.push({ny,nx});
                    g[ny][nx] = false;
                }
            }
        }
    }
    cout<<res<<'\n';
    return;
}

int main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int t;
    cin>>t;
    while(t--) slove();   
    return(0);
}
```

---

### pD 喵喵抓老鼠

[Sprout 43.](http://tioj.sprout.tw/problems/43/)

<br>

**思路**

從喵咪開始多源點 BFS。

Time : $O(TNM)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
#define p pair<int,int>
#define F first
#define S second

void slove(){
    int n,m;
    cin>>n>>m;
    vector<vector<int>>g(n,vector<int>(m));
    queue<p>que;
    char ch;
    for(int i=0;i<n;i++){
        for(int j=0;j<m;j++){
            cin>>ch;
            if(ch=='#'){
                g[i][j] = -1;
            }else if(ch=='K'){
                g[i][j] = -1;
                que.push({i,j});
            }else if(ch=='.'){
                g[i][j] = 0;
            }else if(ch=='@'){
                g[i][j] = 1;
            }
        }
    }
    int dis = 0,cnt = 1;
    vector<p>movef = {{0,-1},{-1,0},{1,0},{0,1}};
    while(!que.empty()){
        auto [y,x] = que.front(); que.pop();
        for(auto [dy,dx] : movef){
            int ny = y+dy,nx = x+dx;
            if(g[ny][nx]==-1) continue;
            if(g[ny][nx]==1){
                cout<<dis+1<<'\n';
                return;
            }
            que.push({ny,nx});
            g[ny][nx] = -1;
        }
        cnt--;
        if(cnt==0){
            cnt = que.size();
            dis++;
        }
    }
    cout << "= =\"\n";
    return;
}

int main(){
    int t;
    cin>>t;
    while(t--) slove();
    return(0);
}
```

---

### pE heap練習

[Sprout 57.](http://tioj.sprout.tw/problems/57/)

<br>

**思路**

直接用 priority_queue，或手棵 min-heap。

Time : $O(N \log N)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    int t;
    cin>>t;
    priority_queue<int,vector<int>,greater<>>pq;
    int a,b;
    for(int i=0;i<t;i++){
        cin>>a;
        if(a==1){
            cin>>b;
            pq.push(b);
        }else if(a==2){
            if(pq.empty()){
                cout<<"empty!\n";
            }else{
                cout<<pq.top()<<'\n';
                pq.pop();
            }
        }
    }
    return(0);
}
```

---

### pF 染色遊戲
[Sprout 45.](https://tioj.sprout.tw/problems/45)

<br>

**思路**

觀察到在 $(a,b)$ 要被 $(c,d)$ 的墨水管染到要到第 $max(|a-c| , |b-d|)$ 秒，所以至多 $N$ 秒所有格子會都是黑色。

每個顏色都可以分成 : 是否有紅色、藍色、黃色，只要有這三項就可以確定是什麼顏色。

如果按秒來記錄上面那件事會到 $O(n^{3})$，因此不妨先預處理那三個顏色在什麼時候會染到，就可以知道相同的東西。

要找某個顏色最多出現的時間，可以直接對每個格子計算在哪個時間區間內會出現，那題目所求就是最多交集數，用差分即可解決。

Time : $O(TN^{2})$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;
#define arr array<int,3>

arr f(char x){
    if(x=='R') return(arr{1,0,0});
    else if(x=='B') return(arr{0,1,0});
    else if(x=='Y') return(arr{0,0,1});
    else if(x=='O') return(arr{1,0,1});
    else if(x=='G') return(arr{0,1,1});
    else if(x=='D') return(arr{1,1,1});
    else if(x=='P') return(arr{1,1,0});
    return(arr{0,0,0});
}

void slove(){
    int n;
    cin>>n;
    vector<vector<arr>>t(n,vector<arr>(n,arr{INT_MAX,INT_MAX,INT_MAX}));
    char ch;
    int x,y;
    for(int k=0;k<3;k++){
        cin>>ch>>x>>y;
        auto c = f(ch);
        for(int i=0;i<n;i++){
            for(int j=0;j<n;j++){
                int ti = max(abs(i-x),abs(j-y));
                if(c[0]) t[i][j][0] = min(ti , t[i][j][0]);
                if(c[1]) t[i][j][1] = min(ti , t[i][j][1]);
                if(c[2]) t[i][j][2] = min(ti , t[i][j][2]);
            }
        }
    }
    cin>>ch;
    auto c = f(ch);
    vector<int>p(n+5,0);
    for(int i=0;i<n;i++){
        for(int j=0;j<n;j++){
            int l=0,r=n;
            for(int k=0;k<3;k++){
                if(c[k]) l = max(t[i][j][k],l);
                else r = min(t[i][j][k]-1,r);
            }
            if(l>r) continue;
            p[l]++;
            p[r+1]--;
        }
    }
    int res = p[0];
    for(int i=1;i<=n+1;i++){
        p[i] += p[i-1];
        res = max(p[i],res);
    }
    cout<<res<<'\n';
}

int main(){
    int t;
    cin>>t;
    while(t--) slove();
    return(0);
}
```

---

### pG 1d-kd-tree
[Sprout 46.](https://tioj.sprout.tw/problems/46)

<br>

**思路**

用 set 維護，搭配二分搜就可以快速找。

Time : $O(N\log N)$

<br>

Code :
```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int n;
    cin>>n;
    string t; int x;
    set<int>s;
    while(n--){
        cin>>t>>x;
        if(t=="insert"){
            s.insert(x);
        }else if(t=="delete"){
            s.erase(x);
        }else if(t=="query"){
            auto it = s.lower_bound(x);
            int a = 1e9+5,b = 1e9+5;
            if(it!=s.begin()) a = *prev(it);
            if(it != s.end()) b = *it;
            
            if(a==1e9+5 || b==1e9+5) cout<<min(a,b);
            else if(x-a == b-x) cout<<a<<" "<<b;
            else if(x-a < b-x) cout<<a;
            else cout<<b;
            cout<<'\n';
        }
    }
    return(0);
}
```

---

### pH 樹重心

[Sprout 263.](https://tioj.sprout.tw/problems/263)

<br>

**思路**

兩次 dfs。

Time : $O(TN)$

<br>

Code : 

```cpp
#include<bits/stdc++.h>
using namespace std;

int n;
vector<vector<int>>g;
vector<int>f;
vector<bool>vis;


int dfs(int v){
    int sz = 1,res = 0;
    for(int u:g[v]){
        if(vis[u]) continue;
        vis[u] = true;
        int x = dfs(u);
        sz += x;
        res = max(x , res);
    }
    f[v] = max(n-sz,res);
    return(sz);
}

void slove(){
    cin>>n;
    g.assign(n,{});
    int v,u;
    for(int i=0;i<n-1;i++){
        cin>>v>>u;
        g[v].push_back(u);
        g[u].push_back(v);
    }
    vis.assign(n,false);
    vis[0] = true;
    f.resize(n);
    dfs(0);

    int ans = 0;
    for(int i=1;i<n;i++){
        if(f[i] < f[ans]) ans = i;
    }
    cout<<ans<<'\n';
}

int main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int t;
    cin>>t;
    while(t--) slove();
    return(0);
}
```

---

### pI Game

[Sprout 364.](https://tioj.sprout.tw/problems/364)

<br>

WIP

--

### pJ 哪裡有卦，哪裡就有源

[Sprout 604.](https://tioj.sprout.tw/problems/604)

<br>

**思路**

判斷是否為二分圖。

Time : $O(TN)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

int n;
vector<vector<int>>g;
vector<int>c;

bool dfs(int v){
    bool res = true;
    for(int u:g[v]){
        if(c[u]!=-1){
            if(c[u]==c[v]) return(false);
            continue;
        }
        c[u] = 1^c[v];
        res &= dfs(u);
    }
    return(res);
}

void slove(){
    cin>>n;
    g.assign(n,{});
    c.assign(n,-1);
    int m,a,b;
    cin>>m;
    for(int i=0;i<m;i++){
        cin>>a>>b;
        g[a].push_back(b);
        g[b].push_back(a);
    }
    bool ans = true;
    for(int i=0;i<n;i++){
        if(c[i]==-1){
            c[i] = 0;
            ans &= dfs(i);
        }
    }
    if(ans) cout<<"NORMAL.";
    else cout<<"RAINBOW.";
    cout<<'\n';
    return;
}


int main(){
    ios::sync_with_stdio(0),cin.tie(0);
    int t;
    cin>>t;
    while(t--) slove();
    return(0);
}
```

---
