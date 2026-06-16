---
layout: post
title: "Week 13"
tags: ["Sprout-2026","list"]
---

---

### pA 164. 謠言問題

[Sprout 164.](https://tioj.sprout.tw/problems/164)

<br>

**思路**

把鎮民當作節點，存在好友關係則當作邊。

可以先以 $K$ 為根節點建 DFS Tree，

那麼當防堵一個節點 $v$ 時，可能會阻隔的只有其子孫，因為要消息要靠祖先傳來，

討論所有 $v$ 的子節點 $u$ 若 $edge(v ,u)$ 是橋，代表若 $v$ 防堵，則 $u$ 的子樹都不會收到消息。

所以可以很直接地對每個點都計算一次這件事，從中取最小值就是答案。

另外，要注意可能不只一個連通塊。

Time : $O(n + m)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

int n,m ,k, t = 1 , cnt;
vector<vector<int>>g;

array<int,2> ans = {0 , -1};
vector<int>sz , dfn, low;

void dfs(int f , int v){
    cnt++;
    dfn[v] = t++; low[v] = dfn[v]; 
    int x = 0;
    for(int u : g[v]){
        if(u==f) continue;
        if(dfn[u]==0){
            dfs(v,u);
            low[v] = min(low[u] , low[v]);
            if(dfn[v] <= low[u]) x += sz[u];
            sz[v] += sz[u];
        }else{
            low[v] = min(dfn[u] , low[v]);
        }
    }
    if(x && v!=k) ans = min(array<int,2> {- x  , v} , ans);
}


int main(){
    cin>>n>>m;
    g.resize(n); sz.resize(n,1); dfn.resize(n); low.resize(n);
    
    int u , v;
    for(int i=0;i<m;i++){
        cin>>u>>v;
        u--; v--;
        g[u].push_back(v); g[v].push_back(u);
    }
    cin>>k; k--;
    dfs(-1 , k);

    if(ans[1]==-1) cout<<0;
    else cout<<ans[1]+1<<" "<<cnt + ans[0];

    return(0);
}
```

---

### pB 167. 高棕櫚傳遞鏈

[Sprout 167.](https://tioj.sprout.tw/problems/167)

<br>

**思路**

輸出一張無向圖中所有割點。

Time : $O(n + m)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

int n , m , t = 1;
vector<vector<int>>g;

vector<int> vis , dfn , low , isAP;

void dfs(int f , int v){
    dfn[v] = t++; low[v] = dfn[v];
    
    int ch = 0;
    for(int u:g[v]){
        if(u==f) continue;
        if(dfn[u]==0){
            ch++;
            dfs(v,u);
            low[v] = min(low[u] , low[v]);
            if(f!=-1 && low[u] >= dfn[v]) isAP[v] = 1;
        }else{
            low[v] = min(dfn[u] , low[v]);
        }
    }
    if(f==-1 && ch>=2) isAP[v] = 1;
}

int main(){
    cin>>n>>m;
    g.resize(n);
    
    int u,v;

    for(int i=0;i<m;i++){
        cin>>u>>v;
        u--; v--;
        g[u].push_back(v);
        g[v].push_back(u);
    }
    
    dfn.resize(n); low.resize(n); isAP.assign(n,0);

    for(int i=0;i<n;i++)
        if(dfn[i]==0) dfs(-1,i);

    for(int i=0;i<n;i++)
        if(isAP[i]) cout<<i+1<<'\n';
    return(0);
}
```

---

### pC 168. 高棕櫚傳遞鏈的逆襲

[Sprout 168.](https://tioj.sprout.tw/problems/168)

<br>

**思路**

輸出一張無向圖中所有橋。

Time : $O(n + m)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

#define p pair<int,int>
#define F first
#define S second

int n,m,t = 1;
vector<vector<p>>g;
vector<p>edge;

vector<int> dfn , low; 
vector<int>Bridge;



void dfs(int f , int v){
    dfn[v] = t++; low[v] = dfn[v];
    for(auto [ u , id] : g[v]){
        if(u==f) continue;
        if(dfn[u]==0){
            dfs(v,u);
            low[v] = min(low[u] , low[v]);
            if(dfn[v] < low[u]) Bridge.push_back(id);
        }else{
            low[v] = min(dfn[u] , low[v]);
        }
    }
}

int main(){
    cin>>n>>m;
    g.resize(n); dfn.resize(n); low.resize(n); edge.resize(m);

    int u , v;
    for(int i=0;i<m;i++){
        cin>>u>>v;
        u--; v--;
        edge[i] = {u,v};
        g[u].push_back({v , i}); g[v].push_back({u , i});
    }

    for(int i=0;i<n;i++)
        if(dfn[i]==0) dfs(-1 , i);

    sort(Bridge.begin(),Bridge.end());
    for(int i:Bridge) cout<<edge[i].F+1<<" "<<edge[i].S+1<<'\n';

    return(0);
}
```

---

### pD 477. 芽芽逛大街

[Sprout 477.](https://tioj.sprout.tw/problems/477)

<br>

**思路**

想要知道以某個節點為起點的路徑中權重和(點權 + 邊權)最大者，

不妨令 $dp[i]$ 表示這件事，那麼 $dp[i] = max(dp[j]) + v[i]$，其中必須存在邊 $i \rightarrow j$ 。

那這作法會有一個很大的問題 : 無法處理環，注意到只要走到環上任一節點就可以走到所有，於是把環上的點縮為一點，就不會有問題，

以上其實也就是 SCC 縮點 + DAG DP。

實作上，同時有邊權、點權有點麻煩，我的做法是將兩者分開處理，細節看程式碼。

Time : $O(n + m)$ 

<br>

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long 
#define p pair<int,int>
#define F first
#define S second

int n , m , t = 1 ,ans = 0;
vector<vector<p>>g , dag;
vector<vector<int>>  scc;
vector<int>s ,  w, dfn , low , in ,id ,  val , dp , vis;
stack<int>stk;

void init(){
    g.resize(n);
    dag.resize(n);
    s.resize(n);
    w.resize(m);
    dfn.resize(n);
    low.resize(n);
    in.resize(n);
    id.resize(n);
    val.resize(n);
    dp.resize(n);
    vis.resize(n);
}

void dfs(int v){
    dfn[v] = t++; low[v] = dfn[v];
    stk.push(v); in[v] = 1;    

    for(auto [ u , x] : g[v]){
        if(dfn[u]==0){
            dfs(u);
            low[v] = min(low[u] , low[v]);
        }else if(in[u]){
            low[v] = min(dfn[u] , low[v]);
        } 
    }

    if(dfn[v]==low[v]){
        scc.push_back({});
        int u;
        do{
            u = stk.top(); stk.pop();
            in[u] = 0;
            scc[scc.size()-1].push_back(u);
            id[u] = scc.size()-1;
        }while(u!=v);
    }
}

void dfs1(int v){
    vis[v] = 1; dp[v] = val[v];
    int nw = 0;
    for(auto [u, x]: dag[v]){
        if(vis[u]==0) dfs1(u);
        nw = max(dp[u]+x , nw);
    }    
    dp[v] += nw;
    ans = max(dp[v] , ans);
}

signed main(){
    cin>>n>>m;
    init();

    for(int i=0;i<n;i++) cin>>s[i];

    int u, v;
    for(int i=0;i<m;i++){
        cin>>u>>v>>w[i];
        u--; v--;
        g[u].push_back({v, i });
    }

    for(int i=0;i<n;i++)
        if(dfn[i]==0) dfs(i);


    for(int i=0;i<scc.size();i++)
        for(int j : scc[i])
            val[i] += s[j];

    for(int i=0;i<n;i++){
        for(auto [j , k] : g[i]){
            if(id[i] == id[j]){
                val[id[i]] += w[k];
            }else{
                dag[id[i]].push_back({id[j] , w[k]});
            }
        }
    }

    for(int i=0;i<scc.size();i++)
        if(vis[i]==0) dfs1(i);
    
    cout<<ans;
    return(0);
}
```

---

### pE. 475. 平衡的技能樹

[Sprout 475.](https://tioj.sprout.tw/problems/475)

<br>

**思路**

觀察到在圖中，在每個簡單環內的權重必須一致，否則從最小、最大的開始選就會不一樣。

等價於找點雙連通分量，並計算其中出現次數最多者，將其它邊權修改。

實作上，我們可以使用 stack 維護目前可能在同個點雙連通分量的邊，注意不要把 forward edge , cross edge (其實就是無向圖裡已經處理過的另一個方向) 重複放，

而當點雙連通分量形成時，將 stack 上方一段取出。

Time : $O(n \log n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

#define p pair<int,int>

int n , m , t = 1 , ans = 0;
vector<vector<p>> g;
stack<int>stk;
vector<int>w,dfn,low; 

void dfs(int f , int v){
    dfn[v] = t++; low[v] = dfn[v];
    int child = 0;
    for(auto [u , id]: g[v]){
        if(u==f) continue;
        if(dfn[u]==0){
            stk.push(id);
            dfs(v , u);
            low[v] = min(low[v] , low[u]);
            if(dfn[v] <= low[u]){
                int nw , sz = 0 , maxi = 0;
                map<int , int>cnt;
                do{
                    nw = stk.top(); stk.pop();
                    sz++;
                    maxi = max(++cnt[w[nw]] , maxi);
                }while(nw!=id);
                if(sz > 1) ans += sz - maxi;
            }
        }else if(dfn[v] > dfn[u]){
            stk.push(id);
            low[v] = min(dfn[u] ,low[v]);
        }
    }
}

int main(){
    cin>>n>>m;
    g.resize(n); w.resize(m); dfn.resize(n); low.resize(n);

    int u , v;
    for(int i=0;i<m;i++){
        cin>>u>>v>>w[i];
        u--; v--;
        g[u].push_back({v , i});
        g[v].push_back({u, i});
    }

    dfs(-1 , 0);

    cout<<ans;

    return(0);
}
```

---

### pF 476. 車水馬龍
[Sprout 476.](https://tioj.sprout.tw/problems/476)

<br>

**思路**

把每個條件分開來處理，

通常在程式中習慣 y 是 row , x 是 column ，笛卡爾對不起。

${(A_{y} , A_{x} ), (B_{y} , B_{x})} $ 

先處理一般的 case : 不同列、不同行

大概會是 :

<img width="361" height="303" alt="image" src="https://github.com/user-attachments/assets/5ab32eab-6dac-4c27-b470-c5e30e30bbad" />

不難發現會有兩種走法，而方向就要由 $A$ , $B$ 相對位置而定。

為了以下方便討論我們用 $p$ , $q$ , $r$ , $s$ 分別代表哪條路及其方向，若最終選擇符合即為 T，相反為 F。

所以交通要求就轉換成 : $(p \land q) \lor (r \land s)$

再透過一些操作

$\equiv (p \lor (r \land s)) \land (q \lor (r \land s))  \equiv (p \lor r) \land (p \lor s) \land (q \lor r) \land (q \lor s)$

就轉換成 2-SAT problem。


不要忘記處理特殊的 case :

同行、同列 : 只需要處理那一項為真即可，方法同上。

Time : $O(n)$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

vector<vector<int>>g;
int t = 1 , nw = 0;
vector<int> id , dfn ,low , in;
stack<int>stk;

void add_edge(int u , int v){
    g[u^1].push_back(v);
    g[v^1].push_back(u);    
}

void init(int sz){
    id.resize(sz);
    dfn.resize(sz);
    low.resize(sz);
    in.resize(sz);
}

void dfs(int v){
    dfn[v] = t++; low[v] = dfn[v];
    stk.push(v); in[v] = 1;
    for(int u : g[v]){
        if(dfn[u]==0){
            dfs(u);
            low[v] = min(low[u] , low[v]);
        }else if(in[u]){
            low[v] = min(dfn[u] , low[v]);
        }
    }

    if(dfn[v]==low[v]){
        int u;
        do{
            u = stk.top(); stk.pop(); in[u] = 0;
            id[u] = nw;
        }while(u!=v);
        nw++;
    }
}

int main(){
    ios::sync_with_stdio(0) , cin.tie(0);
    
    int n , m , k;
    cin>>n>>m>>k;    

    g.resize((n+m)*2);
    
    int Ay , Ax , By , Bx;
    
    for(int i=0;i<k;i++){
        cin>>Ay>>Ax>>By>>Bx;
        Ay = 2*(Ay-1); Ax = 2*n  + 2*(Ax-1); 
        By = 2*(By-1); Bx = 2*n  + 2*(Bx-1);

        if(Ay > By){ Ax^=1; Bx^=1; }
        if(Ax > Bx){ Ay^=1; By^=1; } 
        
        if(Ay==By) g[Ay^1].push_back(Ay);

        if(Ax==Bx) g[Ax^1].push_back(Ax);
        
        if(!(Ax==Bx || Ay==By)){
            add_edge(Ay , By); 
            add_edge(Bx , Ax); 
            add_edge(Ay , Ax);
            add_edge(Bx , By);
        }
    }

    init((n+m)*2);

    for(int i=0;i<2*(n+m);i++)
        if(dfn[i]==0) dfs(i);

    for(int i=0;i<n+m;i++)
        if(id[2*i]==id[2*i+1]){ cout<<"No"; return(0); }
    
    cout<<"Yes\n";
    for(int i=0;i<n+m;i++)
        cout<<(id[2*i] < id[2*i + 1] ? 1 : 0);
    return(0);
}
```

---


### 後記

趁國中畢業後，把題目補一補。

連通性，想法難、實作也難，我輸光光。

- 20260613 人生第一個 top coder (1/1) :
<img width="800" height="200" alt="image" src="https://github.com/user-attachments/assets/2bcd0a00-3c7a-46ad-b90a-68f0cb00ce0d" />

- 人生第一次寫完資芽上機作業 (1/1) :
<img width="800" height="200" alt="image" src="https://github.com/user-attachments/assets/f3d5745f-8122-4570-a1fa-ae86cdc396f5" />
