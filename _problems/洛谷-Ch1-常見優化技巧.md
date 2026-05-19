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

<M一>

枚舉所有可能的 $i$ 值，再數有幾個 $a_{j} = C - a_{i}$，取總和即為答案。

上述計數過程可以使用 map 來輔助計數。

要特別注意 $A = C - A$ 的狀況，題敘說明 $i \ne j$，故要 $-1$。

Time : $O(n \log n)$
<br>

Code

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

<M二>

將 $a$ 排序過後，使用上述做法會發現 : $i$ , $j$ 都是單調不降的。

所以使用雙指針，紀錄目前的配對。

因為陣列中的值可能重複，所以要一次數完所有相同的配對，避免漏算。

Time : $O(n \log n)$ ， 常數較 <M一> 小。

<br>

Code

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
