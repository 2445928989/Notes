## 判有向图中是否有非零环

先处理出强连通分量，对于每一个强连通分量，设定一个点的点权 $f(u)$ 为 $0$，然后开始BFS，对于每条权值为 $w$ 的有向边 $u\rightarrow v$，设 $f(v)=f(u)-w$，如果存在非零环，则必然会在某一点处产生矛盾。

来源：[Obliviate, Then Reincarnate - 题目 - QOJ.ac](https://qoj.ac/contest/1865/problem/9810)

## 边权均分到两端点上

来源：[P4643 [国家集训队] 阿狸和桃子的游戏 - 洛谷](https://www.luogu.com.cn/problem/P4643)

