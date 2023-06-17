---
title: 算法入门
description: 由一道LeetCode题展开，好的算法与差的算法的差异。
categories:
  - 101
tags:
  - CS
date: 2022-11-13 17:31:49
---

LeetCode的第53道题，[最大子数组和](https://leetcode.cn/problems/maximum-subarray/description/)。

> 给你一个整数数组 nums ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

示例：
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为6。

## 方法一：暴力求解

首先是不经过思考，简单的去三重循环，复杂度为O(N³)。

``` C#
public int MaxSubArray(int[] nums)
{
    int max = nums[0];
    int k = nums.Length;
    for (int p = 0; p < k; p++)
    {
        for (int q = p; q < k; q++)
        {
            int r = 0;
            for (int i = p; i <= q; i++)
            {
                r = nums[i] + r;
            }
            if (r > max)
            {
                max = r;
            }
        }
    }
    return max;
}
```
能跑过大部分Case，数据量增大后执行结果超时。

查看代码，可以看到方法一从p到q求和一直在重复计算，于是我们可以使用一个临时的哈希表`temp`来存储`S(p,q)`。复杂度降为O(N²)。
``` C#
public static int MaxSubArray(int[] nums)
{
    int max = nums[0];
    int k = nums.Length;
    Dictionary<string, int> temp = new Dictionary<string, int>();

    for (int p = 0; p < k; p++)
    {
        for (int q = p; q < k; q++)
        {
            int r;
            if (temp.ContainsKey($"{p},{q - 1}"))
            {
                r = temp[$"{p},{q - 1}"] + nums[q];
            }
            else
            {
                r = nums[p];
            }
            if (r > max)
            {
                max = r;
            }
            temp.Add($"{p},{q}", r);
        }
    }
    return max;
}
```
当数组长度来到10000，执行依旧超时，并且占用大量内存空间。进一步分析，可以在23行下方插入`temp.Clear();`或者直接使用 `int temp`代替Dictionary来减少不必要的内存空间，执行仍然超时。

## 方法二：贪心算法

若当前元素之前和小于等于0，则丢弃，若大于0，则加上当前元素。复杂度大大降低，为O(N)。
贪心算法的思想：保证每次操作都是局部最优解。

``` C#
public static int MaxSubArray(int[] nums)
{
    if (nums == null || nums.Length == 0)
    {
        return int.MinValue;
    }
    var max = nums[0];
    var preMax = nums[0];

    for (int i = 1; i < nums.Length; i++)
    {
        if (preMax <= 0)
        {
            preMax = nums[i];
        }
        else
        {
            preMax = nums[i] + preMax;
        }
        if (preMax > max)
        {
            max = preMax;
        }
    }

    return max;
}
```