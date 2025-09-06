---
笔记: 2025-06-29
摘要: https://leetcode.cn/problems/minimum-insertion-steps-to-make-a-string-palindrome/description/
tags:
  - 题解
  - "#高价值"
---
## 思路
先写递归：
定义递归函数：
`int func(string &s, int l, int r)`，其中 l 是当前区间左端点，r是当前区间右端点。
![](https://l4p-bucket-1.oss-cn-shenzhen.aliyuncs.com/img/0b446fd460c0c26abea1ef4dc3995275_MD5.jpeg)

边界情况：当l >= r，代表遍历完毕。
递归的返回值：$[l,r]$ 区间内的最大插入数

~~先看边界，显然当 l == r 时 子串(l,r) 是回文串，当 l + 1 == r 时，子串(l,r)有两种情况：~~
~~1. cl == cr, 子串是回文 -> 可以通过加入 0 个字母构造回文子串~~
~~1. cl != cr，子串不是回文 -> 可以通过加入 1 个字母构造回文子串~~
其实当 l >= r 时，就代表着空串或者 只一个字母，这两种情况都是回文子串。

再看中间，也就是由 cl, 子串, cr 构成的情况。
1. cl == cr -> 无需加入字母
2. cl != cr
	1. 尝试在左侧加入一个字母构造回文子串
	2. 尝试在右侧加入一个字母构造回文子串
		返回两种情况较小的一种

ok！
过用例了，然后TLE了，这是正常的，现在记忆化搜索优化。
ok! AC了，现在转DP
![](https://l4p-bucket-1.oss-cn-shenzhen.aliyuncs.com/img/7552a629f7ee752e655153e3fcb55230_MD5.jpeg)


### 🧩 问题1：`func2` 中的 `memo` 是值传递

`int func2(string &s, vector<vector<int>> memo, int l, int r)`

这是 **值传递**，每次递归调用都会复制一份 `memo`，导致记忆化失效，无法缓存中间结果。

✅ 修复方法：使用**引用传参**

### ❌ **问题2：`func2` 中调用了错误版本的函数 `func`**

`if (s[l] == s[r]) return memo[l][r] = func(s, l + 1, r - 1);`

这仍然调用了**未带 memo 参数**的原始暴力版本 `func`，而不是记忆化版本 `func2`，这会导致子问题不使用缓存，退化为指数复杂度。

### ❌ 问题3：`dp[i][j]` 取值时未加 `+1`

你的这行代码：


`dp[i][j] = s[i] == s[j] ? dp[i + 1][j - 1] : min(dp[i][j - 1], dp[i + 1][j]);`

逻辑正确，但**没有处理插入操作的成本**（每插一次需要 +1）。

✅ **应修改为：**

`dp[i][j] = (s[i] == s[j]) ? dp[i + 1][j - 1] : min(dp[i][j - 1], dp[i + 1][j]) + 1;`

### 解
```cpp
class Solution {
public:
    int func(string &s, int l, int r) {
        if (l > r) return s[l] == s[r] ? 0 : 1;
        if (l == r) return 0;
        if (s[l] == s[r]) return func(s, l + 1, r - 1);
        return min(func(s, l, r - 1) + 1, func(s, l + 1, r) + 1);
    }
    int func2(string &s, vector<vector<int>> &memo, int l, int r) {
        if (l >= r) return 0;
        if (memo[l][r] != -1) return memo[l][r];
        if (s[l] == s[r]) return memo[l][r] = func2(s, memo, l + 1, r - 1);
        return memo[l][r] = min(func2(s, memo, l, r - 1) + 1, func2(s, memo, l + 1, r) + 1);
    }
    int dp(string &s) {
        vector<vector<int>> dp(s.length(), vector<int>(s.length(), 0));

        for (int i = s.length() - 1; i >= 0; --i) {
            for (int j = i; j < s.length(); ++j) {
                if (i == j) continue;
                dp[i][j] = s[i] == s[j] ? dp[i + 1][j - 1] : min(dp[i][j - 1] + 1, dp[i + 1][j] + 1);
            }
        }
        return dp[0][s.length() - 1];
    }
    int minInsertions(string s) {
        // return func(s, 0, s.length() - 1);
        // vector<vector<int>> memo(s.length(), vector<int>(s.length(), -1));
        // return func2(s, memo, 0, s.length() - 1);
        return dp(s);
    }
};
```
