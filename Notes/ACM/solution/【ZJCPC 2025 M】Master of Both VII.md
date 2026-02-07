---
aliases:
  - ACM
tags:
  - ACM
date: 2026-02-07
---
**题目链接：**[Master of Both VII - 题目 - QOJ.ac](https://qoj.ac/contest/2021/problem/10735)

**思路：**
首先我们可以发现这么几个性质
1. 对于每个点，它的度数等于它所属于的三角形数量 $+1$
2. 若一个点的度数为 $2$，代表它在正多边形上相邻的两个点之间一定有连边，而且这个三角形一定能直接从原图形上切下来
3. 对于一个任意图形的三角剖分，一定有至少两个度数为 $2$ 的点，也就是至少有两个能被直接切下来的三角形
4. 我们询问一个点的相邻两个点，就是在询问该点的度数 $-2$ 是多少

那么我们可以这么来构造询问：询问编号为 $[1,n-3]$ 的相邻两点，我们就得到了 $[1, n-3]$ 的度数，我们把 $[n-2,n]$ 的度数设为未知，接下来我们不断利用点度数为 $2$ 的点切下三角形，每切下一个三角形就把相邻两点的度数 $-1$ ，并将该点从图上移除，直到切不动为止

此时根据我们上面的推论 $2$，切不动的时候要么图上只有一个三角形了，要么依然有三个以上的点，在这种情况下，图中一定是还有两个度数为 $2$ 的点的，然而我们现在找不到度数为 $2$ 的点，证明这两个度数为 $2$ 的点一定在 $[n-2,n]$ 范围中。

接下来我们考虑 $[n-2,n]$ 这三个点中，哪两个是度数为 $2$ 的点。我们可以发现，如果 $n-1$ 是度数为 $2$ 的点，那么 $n$ 与 $n-2$ 之间一定有一条连边，也就是 $n$ 与 $n-2$ 的度数一定 $\gt2$ ，这不符合图中有两个度数为 $2$ 的点的要求，所以 $n-1$ 一定不是度数为 $2$ 的点，那么 $n$ 与 $n-2$ 就一定是度数为 $2$ 的点。此时我们考虑这个图的形态，可以发现它一定是这样的图形：除了 $n$ 与 $n-2$，其他点都与 $n-1$ 有一条连边。因为其他形态都违反了三角剖分中边不能交叉的条件。如果不会证，也可以将 $n$ 与 $n-2$ 的度数设为 $2$，并继续用上面不断切三角形的算法流程进行模拟，也能过。

**​代码：**

```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long
void solve() {
    int n;
    cin >> n;
    vector<int> pre(n + 1), nxt(n + 1);
    for (int i = 1; i <= n; i++) {
        pre[i] = i - 1;
        nxt[i] = i + 1;
    }
    pre[1] = n;
    nxt[n] = 1;
    vector<int> du(n + 1, -1);
    for (int i = 1; i <= n - 3; i++) {
        cout << "? " << pre[i] << ' ' << nxt[i] << endl;
        int ret;
        cin >> ret;
        du[i] = ret;
    }
    cout << "! ";
    int cnt = 0;
    vector<int> vis(n + 1);
    while (cnt != n - 3) {
        bool find = 0;
        for (int i = 1; i <= n; i++) {
            if (vis[i])
                continue;
            if (du[i] == 0) {
                find = 1;
                du[pre[i]]--;
                du[nxt[i]]--;
                vis[i] = 1;
                int pr = pre[i], nx = nxt[i];
                nxt[pr] = nx;
                pre[nx] = pr;
                cout << pr << ' ' << nx << ' ';
                break;
            }
        }
        if (find == 0) {
            for (int i = 1; i <= n - 3; i++) {
                if (vis[i])
                    continue;
                cout << n - 1 << ' ' << i << ' ';
            }
            break;
        }
        cnt++;
    }
    cout << endl;
    int ans;
    cin >> ans;
}
signed main() {
    ios::sync_with_stdio(0), cin.tie(0);
    int t = 1;
    cin >> t;
    while (t--) {
        solve();
    }
}
```
