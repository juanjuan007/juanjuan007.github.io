---
layout: post
title: "222F - Expensive Expense"
tags: ["Atcoder"]
---



<br>

**題敘**

在 $N$ 個頂點的樹上，對於每個節點 $i$ 找 $max(dist(i,j) + D_{j})$ 其中 $i \neq j$。

$2 \leq N \leq 2\time 10^{5}$

<br>


---

**思路**

<br>

&lt;M一&gt;

這是一個吃毒的作法。

先選定一個根 $r$，預處理所有的距離 $dist(i,j) + d_{j}$，其中最大值就是答案。

在 dfs 一次，當從節點 $u$ 經過權重 $w$ 的邊走到節點 $v$ 時，會造成 $u$ 子樹內的所有距離減少 $w$，其餘增加 $w$，那再查詢最大值就是答案了。

要對子樹做操作，不妨使用樹壓平變成一個序列。區間查詢極值、區間加值，使用懶惰標記線段樹即可。

Time : $O(n \log^{2} n)$ 

<br>

Code : 

```cpp

```

<br>


&lt;M二&gt;

