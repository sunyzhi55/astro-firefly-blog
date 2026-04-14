---
title: "🚀 快速排序三种实现方式对比分析"
published: 2025-11-28
pinned: true
description: "Compare three different implementations of QuickSort in C++ with analysis."
tags: ["C++", "Algorithm"]
category: "Algorithm"
author: "🔥 Flame"
draft: false
date: 2025-11-28
image: "./images/quickSortCover.png"
pubDate: 2025-11-28
---


# 快速排序三种实现方式对比分析

> **说明**：以下三种方法均基于 C++ 实现，适用于标准快速排序场景。所有代码均可在本地编译运行，并在 Typora 中完美渲染。



| 来源 | 题目/题单                                                    | 说明                     |
| ---- | ------------------------------------------------------------ | ------------------------ |
| 牛客 | https://www.nowcoder.com/practice/3385982ae71d4a1ca8bf3d03614c0325 | **快速排序**             |
| 牛客 | https://www.nowcoder.com/practice/34731183c31e4276b2d081590b2f3a4b | **排序（快速排序）**     |
| 洛谷 | https://www.luogu.com.cn/problem/T588555                     | T588555 快速排序         |
| 洛谷 | https://www.luogu.com.cn/problem/U223702                     | U223702 快速排序         |
| 洛谷 | https://www.luogu.com.cn/problem/T292012                     | T292012 【模板】快速排序 |
| 洛谷 | https://www.luogu.com.cn/problem/T588555                     | T588555 快速排序         |
| 洛谷 | https://www.luogu.com.cn/problem/T318859                     | T318859 Quick Sort快排   |

---

## 一、总体概览

| 特性               | `quickSort1`                  | `quickSort2`           | `quickSort3`                         |
| ------------------ | ----------------------------- | ---------------------- | ------------------------------------ |
| **分区方式**       | 基于值的单向扫描 + 随机 pivot | 双向指针（Hoare 分区） | 三路快排（Dijkstra 三分法）          |
| **Pivot 选择**     | 随机选取元素**值**            | 中间位置元素           | 中间位置元素                         |
| **重复元素优化**   | ❌                             | ❌                      | ✅                                    |
| **平均时间复杂度** | $O(n \log n)$                 | $O(n \log n)$          | $O(n \log n)$，大量重复时接近 $O(n)$ |
| **是否推荐**       | ⚠️ 谨慎                        | ✅ 推荐                 | ✅（修正后）                          |

---

## 二、方法一：`quickSort1` —— 基于值的随机 Partition（Lomuto 变种）

### ✅ 完整可运行代码

```cpp
#include <cmath>
#include <iostream>
using namespace std;
const int N = 1e5 + 5;
int nums[N];

// Partition 函数：将 <= x 的元素移到左边，并确保一个 x 落在最终位置
int Partition(int l, int r, int x) {
  int a = l, xi = l;
  for (int i = l; i <= r; i++) {
    if (nums[i] <= x) {
      swap(nums[a], nums[i]);
      if (nums[a] == x) { xi = a; }
      a++;
    }
  }
  swap(nums[xi], nums[a - 1]);  // 将一个 x 放到正确位置
  return a - 1;
}

void quickSort1(int l, int r) {
  if (l >= r) return;
  // 随机选择一个下标，取其值作为 pivot
  int rand_index = l + rand() % (r - l + 1);
  int x = nums[rand_index];
  int p = Partition(l, r, x);
  quickSort1(l, p - 1);
  quickSort1(p + 1, r);
}

int main() {
  srand(time(0));  // 初始化随机种子
  int n;
  scanf("%d", &n);
  for (int i = 0; i < n; i++) { scanf("%d", &nums[i]); }
  quickSort1(0, n - 1);
  for (int i = 0; i < n; i++) { printf("%d ", nums[i]); }
  return 0;
}
```

### ⚠️ 注意事项
- 虽然引入了随机化，但对**重复元素处理不完善**。
- 在极端重复数据下可能性能退化或行为异常。
- 不建议用于生产环境。

---

## 三、方法二：`quickSort2` —— 经典 Hoare 分区（双向指针）

### ✅ 完整可运行代码

```cpp
#include <cstdio>
#include <iostream>
using namespace std;
const int N = 1e5 + 5;
int nums[N];

void quickSort2(int l, int r) {
  if (l >= r) return;
  int i = l - 1, j = r + 1;
  // int x = nums[l + ((r - l) >> 1)];  // 取中间元素为 pivot
  int x = nums[l + rand() % (r - l + 1)];  // 随机选择一个下标 为 pivot (推荐)
  while (i < j) {
    do { i++; } while (nums[i] < x);
    do { j--; } while (nums[j] > x);
    if (i < j) { swap(nums[i], nums[j]); }
  }
  // 注意：Hoare 分区返回的是 j，递归 [l, j] 和 [j+1, r]
  quickSort2(l, j);
  quickSort2(j + 1, r);
}

int main() {
  int n;
  scanf("%d", &n);
  for (int i = 0; i < n; i++) { scanf("%d", &nums[i]); }
  quickSort2(0, n - 1);
  for (int i = 0; i < n; i++) { printf("%d ", nums[i]); }
  return 0;
}
```

### ✅ 优点
- 代码简洁，效率高。
- 中间选 pivot 对有序/逆序数组表现良好。
- 是经典且广泛使用的快排实现。

---

## 四、方法三：`quickSort3` —— 三路快排（Three-way QuickSort）

> **重要**：原始代码存在严重越界 Bug，以下为**修正后的正确版本**。

### ✅ 完整可运行代码

```cpp
#include <cstdio>
#include <iostream>
using namespace std;

const int N = 1e5 + 5;
int nums[N];

void quickSort3(int l, int r) {
  if (l >= r) return;
  // int x = nums[l + ((r - l) >> 1)];  // pivot 值
  int x = nums[l + rand() % (r - l + 1)];  // 随机生成位置取 pivot 值（推荐）
  int lt = l;                        // [l, lt-1] 为 < x 的区域
  int gt = r;                        // [gt+1, r] 为 > x 的区域
  int i = l;                         // 当前扫描指针

  while (i <= gt) {
    if (nums[i] < x) {
      swap(nums[i], nums[lt]);
      lt++;
      i++;
    } else if (nums[i] > x) {
      swap(nums[i], nums[gt]);
      gt--;
      // 注意：此处 i 不自增，因为从 gt 换来的元素未被检查
    } else {
      i++;  // 等于 x，直接跳过
    }
  }

  // 递归处理 < x 和 > x 的部分，== x 的部分已就位
  quickSort3(l, lt - 1);
  quickSort3(gt + 1, r);
}

int main() {
  int n;
  scanf("%d", &n);
  for (int i = 0; i < n; i++) { scanf("%d", &nums[i]); }
  quickSort3(0, n - 1);
  for (int i = 0; i < n; i++) { printf("%d ", nums[i]); }
  return 0;
}
```

### ✅ 优势场景
- 输入包含**大量重复元素**（如 `[5,5,5,1,5,9,5]`）。
- 时间复杂度可接近 $O(n)$。
- 是处理“重复密集型”数据的最佳快排变种。

---

## 五、总结与使用建议

| 方法         | 适用场景                   | 是否推荐           |
| ------------ | -------------------------- | ------------------ |
| `quickSort1` | 教学演示（展示随机化思想） | ❌ 不推荐（有缺陷） |
| `quickSort2` | 通用排序、竞赛、一般应用   | ✅ 强烈推荐         |
| `quickSort3` | 含大量重复元素的数据       | ✅ 推荐             |

> 💡 **最佳实践**：  
> - 日常使用 → `quickSort2`  
> - 已知数据重复率高 → `quickSort3`（修正版）  
> - 生产环境 → 直接使用 `std::sort`（基于 Introsort，更健壮）

