---
笔记: 2025-07-01
摘要: https://leetcode.cn/problems/burst-balloons/description/
tags:
  - 题解
---
## 思路
> 跟[[lc.1039]] 和 [[lc.1547]]类似，都是枚举划分点

把 两个 值为 1 的气球分别加入数组首尾， 不计入范围区间。
定义，递归函数f(l, r)即范围 l, r 上，当某个气球（枚举划分点）最后打爆时返回的最大贡献。

## 代码分析与修正建议（气球戳破问题 / 最大硬币数）

你目前的目标是求解经典区间 DP 问题 —— LeetCode 312: **Burst Balloons（戳气球）**，当前代码结构是递归 + 记忆化搜索（top-down DP），但存在两个关键问题：

---

### ❌ 问题 1：边界判断错误

```cpp
if (l >= r)
    dp[l][r] = nums[l - 1] * nums[l] * nums[l + 1];
```

此处逻辑错误：

- 当 `l >= r` 时，区间为空或非法，**根本不应该有戳破操作**；
    
- 合理判断应为 `r - l <= 1`，即区间中没有可戳的气球时，代价为 0。
    

---

### ❌ 问题 2：转移方程错误

```cpp
dp[l][r] = max(dp[l][r], f(nums, dp, l, m) + f(nums, dp, m, r) + nums[m]);
```

你在戳气球时，仅加了 `nums[m]`，应为：

- 假设在区间 (l,r)(l, r) 中，**最后一个被戳破的是第 `m` 个气球**；
    
- 戳 `m` 号气球时的得分应是：
    
    nums[l]×nums[m]×nums[r]nums[l] \times nums[m] \times nums[r]
    
    —— 左右两侧是尚未戳破的邻居。
    

---

## ✅ 正确递归写法如下：

```cpp
class Solution {
public:
    int f(vector<int> &nums, vector<vector<int>> &dp, int l, int r) {
        if (l + 1 >= r) return 0; // 区间内无可戳气球
        if (dp[l][r] != -1) return dp[l][r];
        dp[l][r] = 0;
        for (int m = l + 1; m < r; ++m) {
            int coins = nums[l] * nums[m] * nums[r]; // m 是最后戳破的
            coins += f(nums, dp, l, m) + f(nums, dp, m, r);
            dp[l][r] = max(dp[l][r], coins);
        }
        return dp[l][r];
    }

    int maxCoins(vector<int>& nums) {
        vector<int> new_nums(nums.size() + 2, 1);
        for (int i = 0; i < nums.size(); ++i) {
            new_nums[i + 1] = nums[i];
        }
        int n = new_nums.size();
        vector<vector<int>> dp(n, vector<int>(n, -1));
        return f(new_nums, dp, 0, n - 1);
    }
};
```
