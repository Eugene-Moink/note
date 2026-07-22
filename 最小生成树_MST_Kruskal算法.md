# 模板笔记：最小生成树（MST）—— Kruskal 算法

## 1. 适用场景
- 给定一个无向带权图，需要**连通所有节点**。
- 目标是让**连接所有节点的总边权之和最小**。
- 经典考题形式：“用最小的花费把 N 个城市全部连通”、“铺设光缆/网络布线求最低成本”。

## 2. 核心思想（贪心策略）
Kruskal 算法使用的是一种极其简单的贪心策略：
1. 把图中所有的边，按**边权从小到大排序**。
2. 依次从权值最小的边开始检查：
   - 如果这条边的两个端点**还没有连通**（利用并查集 `find` 检查） → 选它，把两个点连起来（`merge`），记录边权。
   - 如果这两个端点**已经连通**了（说明加了这条边会形成环） → 直接跳过。
3. 重复第 2 步，直到选出了 `N - 1` 条边（说明所有点都连通了），或者所有边都检查完。

## 3. 核心模板代码（C++）

```cpp
#include<bits/stdc++.h>
#define endl "\n"
using ll = long long;
using namespace std;

// 1. 结构体存边
struct Edge {
    ll u, v, w;
    // 重载小于号，让 sort 按权值 w 从小到大排序
    bool operator<(const Edge &other) const {
        return w < other.w;
    }
};

const ll MAXN = 200005; // 根据 N 的最大范围设定
ll fa[MAXN];

// 2. 并查集前置模板
void init(ll n) {
    for (ll i = 1; i <= n; i++) fa[i] = i;
}

ll find(ll x) {
    if (fa[x] == x) return x;
    return fa[x] = find(fa[x]);
}

void merge(ll x, ll y) {
    ll fx = find(x), fy = find(y);
    if (fx != fy) fa[fx] = fy;
}

// 3. Kruskal 算法主逻辑
void kruskal() {
    ll n, m;
    cin >> n >> m;
    init(n);

    vector<Edge> edges(m);
    for (ll i = 0; i < m; i++) {
        cin >> edges[i].u >> edges[i].v >> edges[i].w;
    }

    // 核心一：按边权排序
    sort(edges.begin(), edges.end());

    ll ans = 0; // 最小生成树总权值
    ll cnt = 0; // 已连通的边数

    // 核心二：贪心选边
    for (auto &e : edges) {
        // 如果两个端点还没连通
        if (find(e.u) != find(e.v)) {
            merge(e.u, e.v); // 连通它们
            ans += e.w;      // 累加花费
            cnt++;
        }
    }

    // 核心三：判断图是否完全连通
    if (cnt == n - 1) {
        cout << ans << endl;
    } else {
        cout << "orz" << endl; // 不连通的情况
    }
}
```

## 4. 关键注意事项（防坑指南）

- **容器的绑定**：Kruskal 的核心是把 `u`（起点）、`v`（终点）、`w`（权值）绑定在一个结构体里，`sort` 排序时三者会作为一个整体移动，不会错位。
- **排序必须正确**：一定是从小到大排，且必须用并查集判断环。如果忘记排序，贪心策略就会失效。
- **连通性判定**：**`cnt == n - 1` 是必须写的！** 如果图本身不连通（比如有孤立的点），或者边数不足，`cnt` 达不到 `n-1`，此时要按要求输出 `"orz"`（或不连通标记）。

## 5. 补充对比：Prim 算法（作为扩展）
除了 Kruskal，最小生成树还有另一个算法叫 **Prim 算法**。
- **Prim 算法**：从任意一个点出发，用 `priority_queue` 每次挑离当前生成树最近的节点加入。
- **区别对比**：Kruskal 适合 **稀疏图（边数 M 远小于 N²）** 的情况；Prim 适合 **稠密图（边数非常多）** 的情况。作为 1400~1500 分的选手，遇到“连通图求最小花费”时，**优先背熟 Kruskal 模板即可**，因为写起来最快、最不容易出错。😄