---
layout: post
title: "CH2-前綴和、差分與離散化"
tags: ["洛谷","list","-1"]
---

---

### 求區間和

[Luogu P8218](https://www.luogu.com.cn/problem/P8218)

<br>

**思路**

用前綴和維護。

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
    vector<int>num(n),p(n+1);
    for(int i=0;i<n;i++){
        cin>>num[i];
        p[i+1] = p[i]+num[i];
    }
    int m;
    cin>>m;
    int a,b;
    for(int i=0;i<m;i++){
        cin>>a>>b;
        a--; b--;
        cout<<p[b+1]-p[a]<<'\n';
    }
    return(0);
}
```

---

### 最大加權矩形

[Luogu P1719](https://www.luogu.com.cn/problem/P1719)

<br>

**思路**

枚舉矩行的所有可能，方法有兩種。

<br>

&lt;M一&gt;

枚舉所有的左上角、右下角，再用二維前綴和就可以快速計算。

Time : $O(n^{4})$

<br>

Code :

```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long 

signed main(){
    int n;
    cin>>n;
    vector<vector<int>>p(n+1,vector<int>(n+1));
    for(int i=1;i<=n;i++){
        for(int j=1;j<=n;j++){
            cin>>p[i][j];
            p[i][j] = p[i][j] + p[i-1][j] + p[i][j-1] - p[i-1][j-1];
        }
    }

    int ans = 0;

    for(int i=1;i<=n;i++){
        for(int j=1;j<=n;j++){
            for(int k=i;k<=n;k++){
                for(int l=j;l<=n;l++){
                    ans = max(p[k][l] - p[i-1][l] - p[k][j-1] + p[i-1][j-1],ans);
                }
            }
        }
    }
    cout<<ans;
    return(0);
}
```

<br>

&lt;M二&gt;

枚舉矩形中行的起始位置、結束位置，就變成一維最大子陣列和問題。

Time : $O(n^{3})$

<br>

Code : 
```cpp
#include<bits/stdc++.h>
using namespace std;

#define int long long 

signed main(){
    int n;
    cin>>n;
    vector<vector<int>>p(n,vector<int>(n+1,0));
    
    for(int i=0;i<n;i++){
        for(int j=1;j<=n;j++){
            cin>>p[i][j];
            p[i][j] += p[i][j-1];
        }
    }
    int ans = 0;
    for(int i=1;i<=n;i++){
        for(int j=i;j<=n;j++){
            vector<int>x(n);
            int nw = 0;
            for(int k=0;k<n;k++){
                x[k] = p[k][j] - p[k][i-1];
                nw += x[k];
                ans = max(nw , ans);
                nw = max(nw , 0LL);
            }
        }
    }
    cout<<ans;
    return(0);
}
```

---

### 聰明的質檢員
[Luogu P1314](https://www.luogu.com.cn/problem/P1314)

<br>

**思路**
