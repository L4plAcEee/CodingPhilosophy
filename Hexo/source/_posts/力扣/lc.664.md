---
笔记: 2025-07-02
摘要: https://leetcode.cn/problems/strange-printer/description/
tags:
  - 题解
---
> 和 [[P4170]] 是同类型的替身，直接开干。

## 解
```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int strangePrinter(string s) {
        int len = s.length();
        vector<vector<int>> dp(len, vector<int>(len, 0));
        for (int i = 0; i < len; ++i)
            dp[i][i] = 1;
        
        for (int i = len - 1; i >= 0; --i) {
            for (int j = i + 1; j < len; ++j) {
                dp[i][j] = s[i] == s[j] ? dp[i][j - 1] : INT_MAX;
                for (int k = i; k < j; ++k) 
                    dp[i][j] = min(dp[i][j], dp[i][k] + dp[k + 1][j]);
            }
        }
        return dp[0][len - 1];
    }
};
```