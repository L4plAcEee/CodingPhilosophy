---
笔记: 2025-07-02
摘要: https://www.nowcoder.com/practice/e391767d80d942d29e6095a935a5b96b
tags:
  - 题解
---
## 分析
也就是给定一个只由中括号 和 小括号组成的字符串，问如何插入最少的括号令该字符串的所有括号左右配对，例如 `[()` -> `[()]` 或 `[]()`

## 朴素想法
虽然已经提前知道是区间DP，但是还是要好好思考为什么要用区间DP  ->  可以完全地定义问题可能性的状态（参数）简单。

现在就来定义问题的可能性（每种情况）。定义可变参数 l、r，其中 l 为当前区间左端点，r为当前区间右端点。对于处于 l -> r 的区间，我们定义 递归返回值 X（dp数组含义）为区间$[l, r]$ 最少插入  X  个括号即可令该区间内的子串合法。

当 l >= r 时，来到 BaseCase -> 空串一定是合法的（添加 0 个） ，单个括号一定非法（添加 1 个令其合法）

当 来到某个 l , r时，CommonCase：
	如果 sl == sr  -> 该区间的情况依赖于 f(l + 1, r - 1)
	如果 sl != sr -> 该区间的情况依赖于 两种子情况中的最好情况：f(l + 1, r)  || f(1, r  - 1)

> ~~所以直接dp吧！~~


![](https://l4p-bucket-1.oss-cn-shenzhen.aliyuncs.com/img/ddff8e760257890fe053b73961f75fa3_MD5.jpeg)

![](https://l4p-bucket-1.oss-cn-shenzhen.aliyuncs.com/img/1609e61b4d6a291d21ac8a0ec448d8e0_MD5.jpeg)

## 解
```cpp
#include <bits/stdc++.h>
using namespace std;

bool match(char a, char b) {
    return (a == '(' && b == ')') || (a == '[' && b == ']');
}

int f(string &s,vector<vector<int>> &dp, int l, int r) {
    if (l == r) return 1;
    if (l == r - 1) return match(s[l], s[r]) ? 0 : 2;
    if (dp[l][r]  != -1) return dp[l][r];
    int p = match(s[l], s[r]) ? f(s, dp, l + 1, r - 1) : INT_MAX;
    for (int m = l; m < r; ++m) {
        p = min(p, f(s, dp, l ,m) + f(s, dp, m + 1, r));
    }
    return dp[l][r] = p;
}

int dp(string &s) {
    int len = s.length();
    vector<vector<int>> dp(len, vector<int>(len, 0));
    for (int i = 0; i < len; ++i) {
        for (int j = 0; j < len; ++j) {
            if (i == j) dp[i][j] = 1;
            else if (i == j - 1) dp[i][j] = match(s[i], s[j]) ? 0 : 2;
        }
    }
    for (int i = len - 1; i >= 0; --i) {
        for (int j = i + 1; j < len; ++j) {
            dp[i][j] = match(s[i], s[j]) ? dp[i + 1][j - 1] : INT_MAX;
            for (int k = i; k < j; ++k) {
                dp[i][j] = min(dp[i][j], dp[i][k] + dp[k + 1][j]);
            }
        }
    }
    return dp[0][len - 1];
}

int main() {
    string s;
    cin >> s;
    // vector<vector<int>> dp(s.length(), vector<int>(s.length(), -1));
    // cout << f(s, dp, 0, s.length() - 1);
    cout << dp(s) << '\n';
}
// 64 位输出请用 printf("%lld")
```