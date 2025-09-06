---
title: 
date: 2025-07-04
tags:
  - 题解
categories: 
keywords: 
description: 
top_img: 
cover: 
copyright_author: l4place
copyright_author_href: blog.l4place.icu
copyright_url: 
copyright_info: 除特別声明外，本博客所有文章均遵守 WTFPL 许可。
---
> https://leetcode.cn/problems/minimum-cost-to-merge-stones/description/

## 思路
无论怎么说，如果某个输入最终合成一堆石头，也就是说，最后一轮合并一定是 k 堆石头，倒数第二轮合并一定是 k - 1 + k 堆石头，归纳一下可以得出数学公式直接判断是否合法
![image.png](https://l4p-bucket-1.oss-cn-shenzhen.aliyuncs.com/img/20250704200337138.png)

对某种情况的分析，枚举所有可能性
![image.png](https://l4p-bucket-1.oss-cn-shenzhen.aliyuncs.com/img/20250704201128346.png)
![image.png](https://l4p-bucket-1.oss-cn-shenzhen.aliyuncs.com/img/20250704201433215.png)

从 k == 3 到 k == 4 延申到 k == p，从而证明划分结构的正确性。

然后，总结出问题子结构含义，和状态转移方程
![image.png](https://l4p-bucket-1.oss-cn-shenzhen.aliyuncs.com/img/20250704202038652.png)


## 解
```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:

    int mergeStones(vector<int>& stones, int k) {
        int n = stones.size();
        if ((n - 1) % (k - 1) != 0) return -1;
        vector<int> prefix_sum(n + 1, 0);
        for (int i = 0; i < n; ++i) {
            prefix_sum[i + 1] = prefix_sum[i] + stones[i];
        }

        vector<vector<int>> dp(n, vector<int>(n, 0));
        for (int i = n - 2, ans; i >= 0; --i) {
            for (int j = i + 1; j < n; ++j) {
                ans = INT_MAX;

                for (int m = i; m < j; m += k - 1) {
                    ans = min(ans, dp[i][m] + dp[m + 1][j]);
                }

                if ((j - i) % (k - 1) == 0) ans += prefix_sum[j + 1] - prefix_sum[i];
                dp[i][j] = ans;
            }
        }
        return dp[0][n - 1];
    }
};
```