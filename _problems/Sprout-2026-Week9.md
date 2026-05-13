---
layout: post
title: "Week9"
tags: ["Sprout-2026"]
---

### 上機

---

#### 3. 玩電梯
[SproutOJ 369](http://tioj.sprout.tw/contests/61/problems/369)

**思路**

令 $dp[i][j]$ 表搭乘第 $i$ 次電梯後停留在 $j$ 的方法數。
$|y-x| \leq |b-x|$

-abs(b-x) <= y-x <= abs(b-x)


$dp[i][j] = dp[i-1][j]$
