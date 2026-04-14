---
title: "🎯 二分查找完全掌握手册 (五大场景 + 多种写法详解)"
published: 2025-12-23
description: "Comprehensive guide to Binary Search in C++ with multiple scenarios and implementations."
pinned: true
image: './images/binarySearchCover.png'
tags: ["C++", "Algorithm"]
category: "Algorithm"
draft: false 
lang: ''
author: '🔥 Flame'
# slug: binary-search
---
# 🎯 二分查找完全掌握手册 (五大场景 + 多种写法详解)

本笔记系统整理了 **五大经典二分查找场景**，并为每个场景提供了多种核心实现。无论你是初学者还是想巩固深化，都能从中获益。

我们将深入探讨两种主流的实现思路：

1.  **`[l, r]` 闭区间写法**：
    *   **定义**：搜索区间包含左右两个端点，循环条件为 `while (l <= r)`。
    *   **特点**：逻辑直观，每次将搜索空间严格排除 `mid` 后分为两部分 (`l = mid + 1` 或 `r = mid - 1`)。通常需要一个辅助变量 `ans` 来记录满足条件的潜在答案。

2.  **`[l, r)` 左闭右开写法**：
    *   **定义**：搜索区间包含左端点但不包含右端点，循环条件为 `while (l < r)`。
    *   **特点**：代码更紧凑，逻辑与 C++ STL (`lower_bound`, `upper_bound`) 高度一致。当 `mid` 可能是答案时，我们会保留它 (`r = mid`)，最终 `l` (或 `r`) 就是答案所在的位置。



> 参考链接：
>
> https://labuladong.online/algo/essential-technique/binary-search-framework/

---

## 🔑0 前置知识要点

### 🛡️0.1 防止整数溢出
计算 `mid` 时，强烈推荐使用 `l + (r - l) / 2` 的形式，可以有效避免 `l + r` 在数值较大时超出整型上限。

```cpp
// 标准写法 (防溢出)
int mid = l + ((r - l) / 2);

// 在某些场景 (如寻找右边界) 中，需要向上取整，防止死循环
int mid = l + ((r - l + 1) / 2);
```

### 🆚0.2 两种写法的核心区别

| 特性 | 写法 A：`[l, r]` (闭区间) | 写法 B：`[l, r)` (左闭右开) |
| :--- | :--- | :--- |
| 🔁 **初始化** | `l = 0`, `r = nums.size() - 1` | `l = 0`, `r = nums.size()` |
| 🛑 **循环条件** | `while (l <= r)` | `while (l < r)` |
| ⏪ **向左收缩** | `r = mid - 1` (mid 已检查, 排除) | `r = mid` (mid 可能是答案, 保留) |
| ⏩ **向右收缩** | `l = mid + 1` (mid 已检查, 排除) | `l = mid + 1` (mid 已检查, 排除) |

---

## 1️⃣ 1 场景一：精确查找 (Equal)

**🎯 目标**：在数组中找到一个值 `nums[i] == target` 的确切下标。
**↩️ 返回**：如果找到，返回其下标；若不存在，则返回 `-1`。

### 1.1 `[l, r]` 闭区间写法 (推荐)

> **思路解析**：这是最标准的二分查找。在 `[l, r]` 区间内寻找目标。找到 `mid` 后，如果 `nums[mid]` 就是 `target`，则大功告成。如果 `nums[mid]` 小于 `target`，说明目标在右侧，因此下一轮搜索 `[mid + 1, r]`；反之则搜索 `[l, mid - 1]`。

```cpp
int findEqual(vector<int>& nums, int target) {
  int l = 0, r = nums.size() - 1;
  while (l <= r) {
    int mid = l + (r - l) / 2;

    if (nums[mid] == target) {
      return mid;  // 找到直接返回
    } else if (nums[mid] < target) {
      l = mid + 1;
    } else {
      r = mid - 1;
    }
  }
  return -1;
}
```

### 1.2 `[l, r)` 左闭右开写法

> **思路解析**：在 `[l, r)` 区间内寻找。当 `nums[mid] < target` 时，目标一定在 `[mid + 1, r)` 内。当 `nums[mid] > target` 时，目标可能在 `[l, mid)` 内。此写法在未找到目标时，循环终止后 `l` 和 `r` 会重合，但该位置不一定是目标，因此需要额外判断或直接返回 `-1`。

```cpp
int findEqual(vector<int>& nums, int target) {
  int l = 0, r = nums.size();
  while (l < r) {
    int mid = l + (r - l) / 2;

    if (nums[mid] == target) {
      return mid;
    } else if (nums[mid] < target) {
      l = mid + 1;
    } else {
      r = mid;  // 右边界变成 mid
    }
  }
  return -1; // 循环结束时 l == r，但未找到 target
}
```

---

## 2️⃣2 场景二：第一个 `>= target` 的位置 (Lower Bound)

### 2.1 情况一：找不到时返回 `n` (同 C++ STL)

**🎯 目标**：找到 **最小** 的下标 `i`，使得 `nums[i] >= target`。
**↩️ 返回**：若存在，返回该下标；若所有数都小于 `target`，返回数组大小 `n`。

#### 2.1.1 `[l, r]` 闭区间写法

> **写法 1：ans 辅助变量法**
> **思路解析**：当 `nums[mid] >= target` 时，`mid` 是一个潜在答案。我们用 `ans` 记录它，然后向左半部分 `[l, mid - 1]` 进一步压缩，尝试寻找更靠左的好答案。

```cpp
int lowerBound_v1(vector<int>& nums, int target) {
    int n = nums.size();
    int l = 0, r = n - 1;
    int ans = n; // 默认返回 n (越界位置)

    while (l <= r) {
        int mid = l + (r - l) / 2;

        if (nums[mid] >= target) {
            ans = mid;   // 记录当前位置
            r = mid - 1; // 继续往左找
        } else {
            l = mid + 1;
        }
    }
    return ans;
}
```

> **写法 2：混合写法 (闭区间初始化，开区间循环)**
> **思路解析**：这种写法 `while (l < r)` 保证循环结束后 `l == r`。如果 `nums[mid] >= target`，说明答案在 `mid` 或其左侧，所以 `r = mid`；否则答案在右侧，`l = mid + 1`。循环结束后，`l` 指向的就是第一个 `>= target` 的候选位置，最后需要检查该位置是否真的满足条件。

```cpp
int lowerBound_v2(const vector<int>& nums, int target) {
  int n = nums.size();
  int l = 0, r = n - 1; // 注意这里 r 的初始化
  while (l < r) {
    int mid = l + (r - l) / 2;
    if (nums[mid] >= target) {
      r = mid;
    } else {
      l = mid + 1;
    }
  }
  // 循环结束时 l == r，需要检查 nums[l] 是否真的 >= target
  if (nums[l] < target) { return n; }
  return l;
}
```

#### 2.1.2 `[l, r)` 左闭右开写法 (STL 原理)

> **思路解析**：当 `nums[mid] >= target` 时，`mid` 可能是最终答案，所以我们将右边界收缩到 `r = mid` 保留 `mid`。否则，`mid` 太小，去右边找 `l = mid + 1`。循环结束后，`l` 指向的就是第一个 `>= target` 的位置。这是最优雅的 `lower_bound` 实现。

```cpp
int lowerBound_v3(vector<int>& nums, int target) {
  int l = 0, r = nums.size();

  while (l < r) {
    int mid = l + (r - l) / 2;

    if (nums[mid] >= target) {
      r = mid;  // 可能是答案，收缩右边界到 mid
    } else {
      l = mid + 1;
    }
  }
  return l;  // 循环结束时 l == r
}
```

### 2.2 情况二：找不到时返回 `-1`

**🎯 目标**：找到 **最小** 的下标 `i`，使得 `nums[i] == target`（这里原笔记似乎意为找第一个等于，我们按此理解）。
**↩️ 返回**：若不存在，返回 `-1`。

#### 2.2.1 `[l, r]` 闭区间写法

> **写法 1: ans 辅助变量法 + 验证**
> **思路解析**：先用 `ans` 变量找到第一个 `>= target` 的位置，然后验证该位置的值是否恰好等于 `target`。

```cpp
int lowerBound_v1(vector<int>& nums, int target) {
  int n = nums.size();
  int l = 0, r = n - 1;
  int ans = -1;  // 默认返回 -1

  while (l <= r) {
    int mid = l + (r - l) / 2;

    if (nums[mid] >= target) {
      ans = mid;    // 记录当前位置
      r = mid - 1;  // 继续往左找
    } else {
      l = mid + 1;
    }
  }
  // ans 可能是 ≥ target 的第一个位置，需验证是否等于 target
  if (ans != -1 && ans < n && nums[ans] == target) { return ans; }
  return -1;
}
```

> **写法 2: 混合写法 + 验证**
> **思路解析**：同样是先找到候选位置 `l`，然后检查 `nums[l]` 是否等于 `target`。

```cpp
int lowerBound_v2(const vector<int>& nums, int target) {
  int n = nums.size();
  int l = 0, r = n - 1;
  while (l < r) {
    int mid = l + (r - l) / 2;
    if (nums[mid] >= target) {
      r = mid;
    } else {
      l = mid + 1;
    }
  }
  // 如果最后 nums[l] != target，说明应返回 -1
  return nums[l] == target ? l : -1;
}
```

#### 2.2.2 `[l, r)` 左闭右开写法

> **思路解析**：先用标准的 `lower_bound` 找到第一个 `>= target` 的位置 `l`，然后检查该位置是否越界以及值是否等于 `target`。

```cpp
int lowerBound_v3(vector<int>& nums, int target) {
  int l = 0, r = nums.size();
  while (l < r) {
    int mid = l + (r - l) / 2;

    if (nums[mid] >= target) {
      r = mid;  // 可能是答案，收缩右边界
    } else {
      l = mid + 1;
    }
  }
  if (l < 0 || l >= nums.size()) { return -1; }
  return nums[l] == target ? l : -1;
}
```

---

## 3️⃣3 场景三：第一个 `> target` 的位置 (Upper Bound)

**🎯 目标**：找到 **最小** 的下标 `i`，使得 `nums[i] > target`。
**↩️ 返回**：若存在，返回该下标；若所有数都小于等于 `target`，返回数组大小 `n`。

### 3.1 `[l, r]` 闭区间写法

> **写法 1: ans 辅助变量法**
> **思路解析**：逻辑与 `lower_bound` 完全相同，仅将判断条件从 `>=` 改为 `>`。

```cpp
int upperBound_v1(vector<int>& nums, int target) {
  int n = nums.size();
  int l = 0, r = n - 1;
  int ans = n;

  while (l <= r) {
    int mid = l + (r - l) / 2;

    if (nums[mid] > target) {
      ans = mid;    // 记录当前位置
      r = mid - 1;  // 继续往左找
    } else { // nums[mid] <= target
      l = mid + 1;
    }
  }
  return ans;
}
```

> **写法 2: 混合写法**
> **思路解析**：与 `lower_bound` 的混合写法类似，循环结束后 `l` 指向候选位置，最后需要根据 `nums[l]` 的值决定返回 `l` 还是 `l+1`。

```cpp
int upperBound_v2(vector<int>& nums, int target) {
  int n = nums.size();
  if (n == 0) return 0;

  int l = 0, r = n - 1;
  while (l < r) {
    int mid = l + (r - l) / 2;
    if (nums[mid] > target) {
      r = mid;  // 可能是答案
    } else { // nums[mid] <= target
      l = mid + 1;
    }
  }
  // 循环结束 l==r，若 nums[l] > target，l 就是答案，否则答案是 l+1
  return nums[l] > target ? l : l + 1;
}
```

### 3.2 `[l, r)` 左闭右开写法

> **思路解析**：`upper_bound` 的标准优雅实现。判断条件变为 `nums[mid] > target` 时收缩右边界。

```cpp
int upperBound_v3(vector<int>& nums, int target) {
  int l = 0, r = nums.size();

  while (l < r) {
    int mid = l + (r - l) / 2;

    if (nums[mid] > target) {
      r = mid;
    } else {
      l = mid + 1;
    }
  }
  return l;
}
```

---

## 4️⃣4 场景四：最后一个 `<= target` 的位置

**🎯 目标**：找到 **最大** 的下标 `i`，使得 `nums[i] <= target`。
**↩️ 返回**：若存在，返回该下标；若所有数都大于 `target`，返回 `-1`。

### 4.1 `[l, r]` 直接查找法

> **写法 1: ans 辅助变量法**
> **思路解析**：当 `nums[mid] <= target` 时，`mid` 是一个潜在答案。我们用 `ans` 记录它，然后向右半部分 `[mid + 1, r]` 寻找，尝试找到更靠右的好答案。

```cpp
int lastLessEqual_v1(vector<int>& nums, int target) {
  int l = 0, r = nums.size() - 1;
  int ans = -1;  // 默认为 -1

  while (l <= r) {
    int mid = l + (r - l) / 2;

    if (nums[mid] <= target) {
      ans = mid;    // 记录位置
      l = mid + 1;  // 往右找更大的下标
    } else {
      r = mid - 1;
    }
  }
  return ans;
}
```

> **写法 2: 改变 mid 计算方式**
> **思路解析**：这种写法 `mid` 向上取整 `(r - l + 1) / 2`，是为了防止当 `l = r - 1` 时 `mid = l`，若此时 `l = mid` 会导致死循环。当 `nums[mid] <= target`，答案在 `mid` 或其右侧，所以 `l = mid`。

```cpp
int lastLessEqual_v2(vector<int>& nums, int target) {
  int l = 0, r = nums.size() - 1;
  if (nums.empty()) return -1;
  while (l < r) {
    int mid = l + (r - l + 1) / 2; // 向上取整
    if (nums[mid] <= target) {
      l = mid;  // 可能是答案，向右收缩 [mid, r]
    } else {
      r = mid - 1;
    }
  }
  // 循环终止时 l == r，需最后判断
  return (nums[l] <= target) ? l : -1;
}
```

### 4.2 `[l, r)` 转换法 (利用 Upper Bound)

> **💡 核心思想**：
> 寻找 “最后一个 `<= target` 的数”，等价于寻找 “第一个 `> target` 的数” 的 **前一个位置**。这是非常巧妙的转换。

```cpp
int lastLessEqual_v3(vector<int>& nums, int target) {
  // 1. 先用 upper_bound 找到第一个 > target 的位置 l
  int l = 0, r = nums.size();
  while (l < r) {
    int mid = l + (r - l) / 2;
    if (nums[mid] > target) {
      r = mid;
    } else {
      l = mid + 1;
    }
  }
  // 2. 这个位置 l 的前一个位置 (l-1) 就是答案
  // 如果 l=0 (所有数都 > target)，返回 -1，逻辑正确
  return l - 1;
}
```

---

## 5️⃣5 场景五：最后一个 `< target` 的位置

**🎯 目标**：找到 **最大** 的下标 `i`，使得 `nums[i] < target`。
**↩️ 返回**：若存在，返回该下标；若所有数都大于等于 `target`，返回 `-1`。

### 5.1 `[l, r]` 直接查找法

> **思路解析**：与场景四非常相似，仅将条件从 `<=` 改为 `<`。满足 `nums[mid] < target` 时，记录答案并向右寻找。

```cpp
int lastLess_v1(vector<int>& nums, int target) {
    int l = 0, r = nums.size() - 1;
    int ans = -1;

    while (l <= r) {
        int mid = l + (r - l) / 2;

        if (nums[mid] < target) {
            ans = mid;   // 记录位置
            l = mid + 1; // 往右找
        } else { // nums[mid] >= target
            r = mid - 1;
        }
    }
    return ans;
}
```

### 5.2 `[l, r)` 转换法 (利用 Lower Bound)

> **💡 核心思想**：
> 寻找 “最后一个 `< target` 的数”，等价于寻找 “第一个 `>= target` 的数” 的 **前一个位置**。

```cpp
int lastLess_v2(vector<int>& nums, int target) {
    // 1. 先用 lower_bound 找到第一个 >= target 的位置 l
    int l = 0, r = nums.size();
    while (l < r) {
        int mid = l + (r - l) / 2;
        if (nums[mid] >= target) {
            r = mid;
        } else {
            l = mid + 1;
        }
    }
    // 2. 这个位置 l 的前一个位置 (l-1) 就是答案
    return l - 1;
}
```

---

## 📝6 总结速查表

| 场景 | 核心符号 | `[l, r]` 闭区间思路 (ans 记录法) | `[l, r)` 优雅思路 (返回 l 或 l-1) |
| :--- | :---: | :--- | :--- |
| **1. 精确查找** | `==` | 找到 `target` 直接 `return mid` | 找到 `target` 直接 `return mid` |
| **2. 第一个 ≥** (Lower Bound)| `>=` | `ans = mid; r = mid - 1;` (往左压缩) | **直接法**: `r = mid`，返回 `l` |
| **3. 第一个 >** (Upper Bound)| `>` | `ans = mid; r = mid - 1;` (往左压缩) | **直接法**: `r = mid`，返回 `l` |
| **4. 最后一个 ≤** | `<=` | `ans = mid; l = mid + 1;` (往右贪心) | **转换法**: 找 **第一个 `>`** 的位置，返回 `l - 1` |
| **5. 最后一个 <** | `<` | `ans = mid; l = mid + 1;` (往右贪心) | **转换法**: 找 **第一个 `≥`** 的位置，返回 `l - 1` |

> ### 🚀 **记忆口诀**
> *   **找第一个** (>=, >)：满足条件时，答案可能在左边，所以 `ans` 记录，`r` 向左缩；或者用 `[l,r)` 写法，`r = mid`。
> *   **找最后一个** (<, <=)：满足条件时，答案可能在右边，所以 `ans` 记录，`l` 向右扩；或者**优雅地转换为找 “第一个不满足条件的” 的前一个位置**。





## 🛠️7 C++ STL 实现



C++ 标准库 `<algorithm>` 提供了两个强大的函数，`lower_bound` 与 `upper_bound`，完美实现了我们之前讨论的场景，且效率极高。**它们都要求作用的区间是已排序的**。

### 📌 7.1`lower_bound`

-   **功能**：查找第一个 **不小于 (>=)** `value` 的元素。
-   **对应场景**：场景二，找到第一个 `>= target` 的位置。
-   **函数签名**：`ForwardIterator lower_bound(ForwardIterator first, ForwardIterator last, const T& value);`
-   **参数**：
    -   `first`: 指向序列起始位置的迭代器。
    -   `last`: 指向序列**结束位置之后**的迭代器（构成 `[first, last)` 左闭右开区间）。
    -   `value`: 要查找的值。
-   **返回值**：返回一个指向目标的迭代器。如果所有元素都小于 `value`，则返回 `last`。

示例代码

```cpp
#include <algorithm>  // 包含 lower_bound
#include <iostream>
#include <vector>
using namespace std;
int main() {
  vector<int> nums = {10, 20, 30, 30, 30, 40, 50};
  int target = 30;

  // 查找第一个 >= target 的元素
  auto it = lower_bound(nums.begin(), nums.end(), target);

  if (it != nums.end()) {
    // 直接使用迭代器减法计算下标（适用于随机访问迭代器）
    long long index = it - nums.begin();
    cout << "第一个 >= " << target << " 的元素是: " << *it << endl;
    cout << "它的下标是: " << index << endl;  // 输出下标 2
  } else {
    cout << "没有找到 >= " << target << " 的元素。" << endl;
  }
  return 0;
}
```

### 📌7.2 `upper_bound`

-   **功能**：查找第一个 **严格大于 (>)** `value` 的元素。
-   **对应场景**：场景三，找到第一个 `> target` 的位置。
-   **函数签名**：`ForwardIterator upper_bound(ForwardIterator first, ForwardIterator last, const T& value);`
-   **参数**：同 `lower_bound`。
-   **返回值**：返回一个指向目标的迭代器。如果所有元素都小于等于 `value`，则返回 `last`。

示例代码

```cpp
#include <algorithm>
#include <iostream>
#include <vector>
using namespace std;
int main() {
  vector<int> nums = {10, 20, 30, 30, 30, 40, 50};
  int target = 30;

  // 查找第一个 > 30 的元素
  auto it = upper_bound(nums.begin(), nums.end(), target);

  if (it != nums.end()) {
    long long index = it - nums.begin();
    cout << "第一个 > " << target << " 的元素是: " << *it << endl;  // 输出 40
    cout << "它的下标是: " << index << endl;  // 输出下标 5
  } else {
    cout << "没有找到 > " << target << " 的元素。" << endl;
  }
  return 0;
}
```





