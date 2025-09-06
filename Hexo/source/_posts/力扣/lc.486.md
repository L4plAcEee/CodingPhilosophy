---
笔记: 2025-06-29
摘要: https://leetcode.cn/problems/predict-the-winner/description/
tags:
  - 题解
---
## 思路
和[[lc.1312]]类似，所以直接上DP
~~对于情况的定义，就是区间内player1 和player2得分的差值。~~

### ❌ 当前代码的问题：

你的状态定义：

`dp[i][j] = max(dp[i+1][j] + nums[i], dp[i][j-1] + nums[j]);`

看似表示从区间 `[i, j]` 中，当前玩家选左边或右边所能获得的最大分数，但**这个定义忽略了对手也在博弈，且是理性人**，会让你得分最少。

## ✅ 正确做法：记忆化搜索或区间DP（含对手最优行为）

### 思路说明：

令 `dp[i][j]` 表示**从子数组，先手比后手多拿多少分**。

- 若先手拿 `nums[i]`，对手在 `[i+1, j]` 先手 => 剩下得分为 `nums[i] - dp[i+1][j]`
    
- 同理，拿 `nums[j]` 得 `nums[j] - dp[i][j-1]`
    

`dp[i][j] = max(nums[i] - dp[i+1][j], nums[j] - dp[i][j-1]);`


## 解
```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:

    int dp(vector<int> &nums) {
        vector<vector<int>> dp(nums.size(), vector<int>(nums.size(), 0));
        for (int i = 0; i < nums.size(); ++i) dp[i][i] = nums[i];

        for (int i = nums.size() - 1; i >= 0; --i) {
            for (int j = i; j < nums.size(); ++j) {
                if (i == j) continue;
                dp[i][j] = max(-dp[i + 1][j] + nums[i], -dp[i][j - 1] + nums[j]);
            }
        }
        return dp[0][nums.size() - 1];
    }
    bool predictTheWinner(vector<int>& nums) {
        
        return dp(nums) >= 0;
    }
};
```