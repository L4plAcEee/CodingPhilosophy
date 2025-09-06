---
笔记: 2025-07-02
摘要: Codeforces Round 1034 (Div. 3)
tags:
  - 题解
---
> md这么简单的题目给我绕死了。
## 解析
给定一个 数组 $a_n$, 有 n 个玩家， 第 i 个玩家的 力量是 $a_i$ ，每当还有剩余超过 k 名玩家时，随机选择两个玩家，对比力量值，淘汰较小的（若相同则随机淘汰一个），求 给定一个 玩家编号 j， 是否可以成为最后剩余的 k 名玩家。

即淘汰人数为 n - k 名。

情况一：~~j 的力量值就排前 k 名~~（有点弱智了）

情况二：（田忌赛马）让 j 去淘汰 比 j 力量值小的，让比 j 力量值大的 去淘汰 处于该玩家和 j 中间的 玩家。

## 完整思路
设所有的力量值小于等于 a_j 的玩家为 集合 S_less 设所有力量值大于 a_j 的玩家为集合 S_greater ，所有淘汰的人数为 n - k 名。S_less 中的所有玩家均由 a_j 淘汰，贡献 S_less.size 个淘汰额，剩余的淘汰额则需要从 S_greater 中获取。构建一个生存策略：总是让某个强者和次强者两两内耗。至少 S_greater会剩下一名玩家，即最多贡献 S_greater.size - 1 个淘汰额。如果这两个贡献加在一起依然无法让 a_j 进入 k 名，则 a_j必然被淘汰。

也就是说当 k > 1的时候总能存活，如果当 k == 1 的时候，不是最大值就必然不能存活


![](https://l4p-bucket-1.oss-cn-shenzhen.aliyuncs.com/img/fd9765d46e6cdd733b43dba7226ca928_MD5.jpeg)

## 解
```cpp
#include <bits/stdc++.h>
using namespace std;

int a[200005];

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int t; cin >> t;
    while (t--) {
        int n, j, k;
        cin >> n >> j >> k;

        int max_val = 0;
        for (int i = 0; i < n; ++i) {
            cin >> a[i];
            max_val = max(max_val, a[i]);
        }
        if (k > 1) {
            cout << "Yes" << '\n';
            continue;
        }
        int aj = a[j - 1];
        cout << (aj == max_val ? "Yes" : "No") << '\n';
    }
}

```