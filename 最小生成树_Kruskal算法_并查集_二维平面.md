# ABC 065 D - Built? 笔记

## 适用场景
- 二维平面内 \(N\) 个点，建路代价为 \(\min(|x_i-x_j|, |y_i-y_j|)\)，求连通全部点的最小代价。
- 数据范围较大（\(N \le 2 \times 10^5\)），无法暴力建 \(N^2\) 条边。
- 核心底层算法：Kruskal 最小生成树 + 并查集（DSU）。

## 核心模板代码
```cpp
#include<bits/stdc++.h>
#define endl "\n"
using ll = long long;
using namespace std;

struct DSU
{
    vector<ll> parent, size;
    DSU(ll n)
    {
        parent.resize(n + 1);
        size.resize(n + 1, 1);
        for (ll i = 0; i <= n; i++) parent[i] = i;
    }

    ll find(ll x)
    {
        if (parent[x] == x) return x;
        return parent[x] = find(parent[x]);
    }

    bool unite(ll x, ll y)
    {
        x = find(x); y = find(y);
        if (x == y) return false;
        if (size[x] < size[y]) swap(x, y);
        parent[y] = x;
        size[x] += size[y];
        return true;
    }
};

struct Town { ll x, y; };
struct Edge { ll u, v, cost; };

void moink()
{
    ll n;
    cin >> n;
    vector<Town> towns(n);
    for (ll i = 0; i < n; i++) cin >> towns[i].x >> towns[i].y;

    vector<ll> order(n);
    iota(order.begin(), order.end(), 0);
    vector<Edge> edges;

    // 按 X 排序加相邻边
    sort(order.begin(), order.end(), [&](ll a, ll b) { return towns[a].x < towns[b].x; });
    for (ll i = 0; i < n - 1; i++)
    {
        ll u = order[i], v = order[i + 1];
        edges.push_back({u, v, towns[v].x - towns[u].x});
    }

    // 按 Y 排序加相邻边
    sort(order.begin(), order.end(), [&](ll a, ll b) { return towns[a].y < towns[b].y; });
    for (ll i = 0; i < n - 1; i++)
    {
        ll u = order[i], v = order[i + 1];
        edges.push_back({u, v, towns[v].y - towns[u].y});
    }

    // Kruskal 算法
    sort(edges.begin(), edges.end(), [&](const Edge &a, const Edge &b) { return a.cost < b.cost; });

    DSU dsu(n);
    ll ans = 0, cnt = 0;
    for (auto &e : edges)
    {
        if (dsu.unite(e.u, e.v))
        {
            ans += e.cost;
            if (++cnt == n - 1) break;
        }
    }
    cout << ans << endl;
}
```

## 关键注意事项（避坑指南）
- **核心思维跨越**：平面上所有点两两建边会 TLE。**只需将点分别按 X 和 Y 排序，仅连接相邻两点**，边数立即从 \(N^2\) 降至 \(2N\)。
- **索引数组（必须）**：不能直接对 `towns` 数组排序。必须用 `vector<ll> order(n)` 记录下标，排序 `order`，通过 `towns[order[i]]` 访问坐标，否则建边时会丢失原城镇编号 `u` 和 `v`。
- **计算边权**：排序后，后面的坐标一定大于等于前面的坐标，直接用 `大 - 小` 即可，**无需额外写 `abs()`**。
- **提前退出**：连通 \(N\) 个点只需 \(N-1\) 条边。一旦 `cnt == n - 1`，必须立即 `break`。
- **防溢出**：**坐标差值及总答案必须使用 `long long`**（即代码中的 `using ll = long long;`）。