# 判有向图中是否有非零环

先处理出强连通分量，对于每一个强连通分量，设定一个点的点权 $f(u)$ 为 $0$，然后开始BFS，对于每条权值为 $w$ 的有向边 $u\rightarrow v$，设 $f(v)=f(u)-w$，如果存在非零环，则必然会在某一点处产生矛盾。

来源：[Obliviate, Then Reincarnate - 题目 - QOJ.ac](https://qoj.ac/contest/1865/problem/9810)

# 边权均分到两端点上

来源：[P4643 [国家集训队] 阿狸和桃子的游戏 - 洛谷](https://www.luogu.com.cn/problem/P4643)

# 将排列交换相邻两项得到目标排列的次数

我们考虑将 $(1,2,3,4,5,6,\dots,n)$ 变换为 $P=(P_0,P_1,P_2,P_3,P_4,P_5,P_6,\dots,n)$ 发现每次贪心地交换都会使逆序对数+1 ，于是次数就是 $P$ 的逆序对数 (可以考虑冒泡排序对逆序对数的影响，问题等价)

来源：[F - Simple Operations on Sequence](https://atcoder.jp/contests/abc232/tasks/abc232_f?lang=en)

