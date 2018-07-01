---
title: 852.Peak Index in a Mountain Array
description: 给定一个形似『山峰』的数组，求其『顶点』的索引。
categories: LeetCode
tags:
  - LeetCode
  - 二分查找
date: 2018-07-01 17:05:26
updated: 2018-07-01 17:05:26
---

[LeetCode: 852.Peak Index in a Mountain Array](https://leetcode.com/problems/peak-index-in-a-mountain-array)

# 题目
数组`A`是一个『山峰』，其必满足这两个属性：
1. A.length >= 3
2. 存在一个`i`，`0 < i < A.length - 1`，满足：`A[0] < A[1] < ... A[i-1] < A[i] > A[i+1] > ... > A[A.length - 1]`

给定一个是『山峰』的数组，返回『顶点』`i`。

# 样例
1. Example 1:
> Input: [0,1,0]
> Output: 1

2. Example 2:
> Input: [0,2,1,0]
> Output: 1

# 分析
首先，可以使用『二分查找』。
其次，『山峰』有一个很重要的属性：`A[i-1] < A[i] > A[i+1]`，都是大于或者小于，没有等于。

对于`A[mid]`，比较A[mid]与A[mid+1]的大小，
1. 如果`A[mid] < A[mid+1]`，那么是`/`这种形状的斜坡，则`low = mid+1`
2. 如果`A[mid] > A[mid+1]`，那么是`\`这种形状的斜坡，则`high = mid`

# 代码
C++ 代码如下：
```C++
int peakIndexInMountainArray(vector<int>& A) {    
    int low = 0, high = A.size() - 1;
    while (low < high)
    {
        int mid = (low + high) / 2;
        if (A[mid] < A[mid+1])  // 因为low < high，所以mid+1 <= high
        {
            low = mid + 1;
        }
        else  // A[mid] >= A[mid+1]
        {
            high = mid;
        }     
    }
    return high;
}
```
