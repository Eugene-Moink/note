
# 模板笔记：DFS 枚举三大题型独立框架

## 1. 枚举元组（B3621）—— 可重复选择

### 适用场景

数据范围小，允许元素重复选择，只需生成 \(n\) 个位置、每个位置范围 1~k 的全部组合。

### 核心模板

```cpp
#include<bits/stdc++.h>
using namespace std;

int n, k;
int path[20];

void dfs(int step)
{
    if (step == n) // 填满 n 个空位
    {
        for (int i = 0; i < n; i++)
        {
            cout << path[i];
            if (i < n - 1) cout << " ";
        }
        cout << endl;
        return;
    }

    for (int i = 1; i <= k; i++) // 范围是 k
    {
        path[step] = i;
        dfs(step + 1);           // 下一轮循环自动覆盖旧值，无需回溯
    }
}
```

### 关键注意事项

- 递归边界是 `step == n`。
- 不需要 `used[]` 标记数组。
- 不需要写恢复现场（回溯）。

## 2. 枚举子集（B3622）—— 选 / 不选

### 适用场景

每个元素只有“选（Y）”或“不选（N）”两种状态，输出所有方案。

### 核心模板

```cpp
#include<bits/stdc++.h>
using namespace std;

int n;
char path[20];

void dfs(int step)
{
    if (step == n) // 所有 n 个元素决策完毕
    {
        for (int i = 0; i < n; i++) cout << path[i];
        cout << endl;
        return;
    }

    char choices[2] = {'N', 'Y'}; // 先 N 后 Y，保证字典序
    for (int i = 0; i < 2; i++)
    {
        path[step] = choices[i];
        dfs(step + 1);
        // 无需恢复现场，天然覆盖
    }
}
```

### 关键注意事项

- **禁止使用** `for (char i = 'N'; i <= 'Y'; i++)`，否则 `char` 溢出会导致死循环（TLE）。
- 输出字符串时，**不加空格**。
- 字典序通过枚举顺序 `'N'` 在 `'Y'` 之前实现。

## 3. 枚举排列（B3623）—— 不可重复选择

### 适用场景

从 \(n\) 个候选人里选出 \(k\) 个排成一列，数字**不能重复使用**。

### 核心模板

```cpp
#include<bits/stdc++.h>
using namespace std;

int n, k;          // n 个候选人，选 k 个排列
int path[20];
bool used[20];     // 标记数字是否已被占用

void dfs(int step)
{
    if (step == k) // 队伍已经排满 k 个人
    {
        for (int i = 0; i < k; i++)
        {
            cout << path[i];
            if (i < k - 1) cout << " ";
        }
        cout << endl;
        return;
    }

    for (int i = 1; i <= n; i++) // 从 n 个候选中挑一个
    {
        if (used[i]) continue; // 已被选过则跳过

        used[i] = true;        // 标记占用
        path[step] = i;
        dfs(step + 1);
        used[i] = false;       // 【回溯】恢复现场，让别的分支能用该数字
    }
}
```

### 关键注意事项

- 递归边界是 `step == k`（队伍长度），不是 `n`。
- 循环范围必须从 `1` 到 `n`（候选总数）。
- `used[i] = false;` 必须写在递归调用 `dfs(step + 1);` 之后。

## 核心特性对照表

| 题型               | 递归边界      | 循环范围      | 特殊数组            | 输出格式       | 是否需回溯                  |
| :----------------- | :------------ | :------------ | :------------------ | :------------- | :-------------------------- |
| **枚举元组** | `step == n` | `1 ~ k`     | 仅`path`          | 数字空格分隔   | ❌ 不需要                   |
| **枚举子集** | `step == n` | 固定 2 种字符 | 仅`path`          | 连续字符无空格 | ❌ 不需要                   |
| **枚举排列** | `step == k` | `1 ~ n`     | `path` + `used` | 数字空格分隔   | ✅ 必须写`used[i]=false;` |
