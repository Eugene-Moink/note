# `is_sorted()` 用法笔记

---

## 1. 头文件

```cpp
#include <algorithm>  // 或直接 #include <bits/stdc++.h>
```

---

## 2. 函数原型

```cpp
bool is_sorted(ForwardIt first, ForwardIt last);
```

- `first`：指向区间起始位置的迭代器（如 `a.begin()`）
- `last`：指向区间末尾**后一个位置**的迭代器（如 `a.end()`）

---

## 3. 功能

检查区间 `[first, last)` 内的元素是否已经按 **非递减** 顺序排列（即升序，允许相等）。

**判断逻辑**：对于区间内所有相邻元素，检查是否满足：

```
a[i] <= a[i+1]   （对全部 i 成立）
```

只要有一处违反（即 `a[i] > a[i+1]`），就返回 `false`。

---

## 4. 返回值

| 情况 | 返回值 |
|------|--------|
| 所有相邻元素满足 `<=` | `true`（1） |
| 存在相邻元素 `>` | `false`（0） |
| 区间为空（`first == last`） | `true` |
| 区间只有一个元素 | `true` |

---

## 5. 示例代码

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<int> a = {1, 2, 2, 3};   // 非递减
    vector<int> b = {1, 3, 2, 4};   // 3 > 2，降序
    vector<int> c = {5};            // 单元素

    cout << is_sorted(a.begin(), a.end()) << '\n';  // 输出 1
    cout << is_sorted(b.begin(), b.end()) << '\n';  // 输出 0
    cout << is_sorted(c.begin(), c.end()) << '\n';  // 输出 1
    return 0;
}
```

---

## 6. 注意事项

| 注意点 | 说明 |
|--------|------|
| **非递减 ≠ 严格递增** | 等号是允许的（`1,1,2` 也算非递减） |
| **迭代器必须合法** | `begin()` 和 `end()` 必须来自同一容器 |
| **时间复杂度** | O(n)，线性扫描，非常快 |
| **降序检测** | 如果数组是降序（如 `[5,4,3]`），直接返回 `false` |

---

## 7. 竞赛中的典型用法

**场景**：判断当前数组是否已经有序，根据结果决定下一步。

```cpp
if (is_sorted(a.begin(), a.end())) {
    // 已经有序，不需要操作
} else {
    // 否则进行某种处理（如排序、删除等）
}
```

**例子（本场题目）**：
- 数组已有序 → 答案 `n`（全保留）
- 否则 → 答案 `1`（删到只剩一个）

---

## 8. 手动等价写法（以防不能用 STL）

```cpp
bool sorted = true;
for (int i = 0; i + 1 < n; i++) {
    if (a[i] > a[i+1]) {
        sorted = false;
        break;
    }
}
```

---

## 9. 总结一句话

`is_sorted` 是用来检查数组是否已经排好序（升序）的快捷工具，常用于思维题中快速判断是否需要排序或特殊处理。