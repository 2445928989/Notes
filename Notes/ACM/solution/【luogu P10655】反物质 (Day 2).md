---
aliases:
  - ACM
tags:
  - ACM
date: 2025-11-18T13:01:00
---
**题目链接：**[P10655 [ROI 2017] 反物质 (Day 2) - 洛谷](https://www.luogu.com.cn/problem/P10655)

**思路：**
首先列出dp状态和转移方程

由于需要考虑反物质不能超过容量$a$，正着dp比较麻烦，所以可以倒着来，用类似期望dp的方式。

设 $f_i$ 代表当容器内反物质有 $i$ 个时，通过后续操作能得到的最大金钱数，则转移方程为：

$$
f_i=\max(\underset{1\leq j\leq n,i+r_j\leq a}\max(\underset{l_j\leq t\leq r_j}\min(f_t)-c_j),1e9\times i)
$$
发现瓶颈在对一个区间取$\min$，如果直接朴素统计是$\mathcal{O}(na^2)$的，考虑优化，比较好想的就是用单调队列维护$n$个区间的最小值，但是发现空间会被卡到$\mathcal{O}(na)$，爆了

考虑单调队列的运行流程，总是从左边取一个值放入队首，然后删掉队尾大于队首元素的元素，并检查队尾元素是否超过了区间的约束，此时区间内最小元素一定是队尾的元素。我们考虑一个元素不再是这个队列的最小元素，必然是因为要么这个元素超过了区间的约束，要么是因为有一个更小的元素进入了队列。

于是我们可以用一个单调栈来记录每个元素左侧第一个比自己更小的元素，再对于每一种实验维护一个当前取到的最小元素是谁，它发生改变当且仅当

1. 该元素被区间右端点越过超过了区间的约束
2. 该元素左侧那个比自己小的元素进入了区间

于是倒着dp，一边单调栈处理一边维护每种操作目前取到的最小元素即可。

时间复杂度$\mathcal{O}(na)$，空间复杂度$\mathcal{O}(n+a)$。


**​代码：**

```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long
void solve()
{
    int n, a;
    cin >> n >> a;
    vector<array<int, 3>> v(n);
    for (int i = 0; i < n; i++)
    {
        cin >> v[i][0] >> v[i][1] >> v[i][2];
    }
    int t = 1e9;
    vector<int> dp(a + 1);
    vector<int> now(n, a);
    vector<int> pre(a + 1);
    stack<int> st;
    for (int i = a; i >= 0; i--)
    {
        dp[i] = t * i;
        int mx = 0;
        for (int j = 0; j < n; j++)
        {
            if (i + v[j][1] > a)
                continue;
            if (now[j] > i + v[j][1])
                now[j] = i + v[j][1];
            while (pre[now[j]] >= i + v[j][0])
            {
                now[j] = pre[now[j]];
            }
            mx = max(mx, dp[now[j]] - v[j][2]);
        }
        dp[i] = max(mx, dp[i]);
        while (!st.empty() && dp[st.top()] > dp[i])
        {
            pre[st.top()] = i;
            st.pop();
        }
        st.push(i);
    }
    cout << dp[0] << '\n';
}
signed main()
{
    ios::sync_with_stdio(0), cin.tie(0);
    int t = 1;
    // cin >> t;
    while (t--)
    {
        solve();
    }
}
```
