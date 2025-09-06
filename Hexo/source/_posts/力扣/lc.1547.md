---
笔记: 2025-07-01
摘要: 
tags:
  - 题解
---
## 思路
和[[lc.1039]]类似
都是区间DP题型中，枚举划分点的存在。
本题要先把 0 和 n 视为端点并加入 cuts然后排序,最后对 cuts 进行划分点枚举区间DP才是正解。

##
```cpp
class Solution {
public:
    int f(vector<int> &cuts, vector<vector<int>> &dp, int l, int r) {
        if (dp[l][r] != -1) return dp[l][r];
        if (l + 1 >= r) return dp[l][r] = 0; // 相邻或相等时无法再切割
        dp[l][r] = INT_MAX;
        for (int m = l + 1; m < r; ++m) {
            dp[l][r] = min(dp[l][r], f(cuts, dp, l, m) + f(cuts, dp, m, r) + cuts[r] - cuts[l]);
        }
        return dp[l][r];
    }

    int minCost(int n, vector<int>& cuts) {
        cuts.push_back(0);
        cuts.push_back(n);
        sort(cuts.begin(), cuts.end());
        int sz = cuts.size();
        vector<vector<int>> dp(sz, vector<int>(sz, -1));
        return f(cuts, dp, 0, sz - 1);
    }
};

```