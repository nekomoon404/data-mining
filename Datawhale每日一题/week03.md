Datawhale每日一题打卡 week03 

实现语言：C++

## 06-01
### LC1744. 你能在你最喜欢的那天吃到你最喜欢的糖果吗？

[力扣](https://leetcode-cn.com/problems/can-you-eat-your-favorite-candy-on-your-favorite-day/)

给你一个下标从 0 开始的正整数数组 `candiesCount` ，其中 `candiesCount[i]` 表示你拥有的第 `i` 类糖果的数目。同时给你一个二维数组 `queries` ，其中 `queries[i] = [favoriteTypei, favoriteDayi, dailyCapi]` 。

你按照如下规则进行一场游戏：

- 你从第 0 天开始吃糖果。
- 你在吃完 所有 第 `i - 1` 类糖果之前，不能 吃任何一颗第 `i` 类糖果。
- 在吃完所有糖果之前，你必须每天 至少 吃 一颗 糖果。

请你构建一个布尔型数组 `answer` ，满足 `answer.length == queries.length` 。 `answer[i]` 为 true 的条件是：在每天吃 不超过 `dailyCapi` 颗糖果的前提下，你可以在第 `favoriteDayi` 天吃到第 `favoriteTypei` 类糖果；否则 `answer[i] 为 false` 。注意，只要满足上面 3 条规则中的第二条规则，你就可以在同一天吃不同类型的糖果。

请你返回得到的数组 answer 。

数据范围：

- `1 <= candiesCount.length <= 10^5`
- `1 <= candiesCount[i] <= 10^5`
- `1 <= queries.length <= 10^5`
- `queries[i].length == 3`
- `0 <= favoriteTypei < candiesCount.length`
- `0 <= favoriteDayi <= 10^9`
- `1 <= dailyCapi <= 10^9`

<div  align="center">  
<img src="https://gitee.com/nekomoon404/blog-img/raw/master/img/QQ图片20210601161858.png" width=50% />
</div>


【思路——前缀和】

设每个query中 `idx = query[0]` ， `day = query[1] + 1` ， `cnt = query[2]` ，即每天能吃到的糖果数量是 $[1, cnt]$之间，如果我们能在第day天吃到第idx种糖果，则可以找到一种吃法在day天能吃完**第idx之前的所有糖果**，且第day天能吃到第idx种糖果。可以转换成**求能吃到第idx种糖果需要的最少天数和最多天数**，如果day在它们之间，则可以返回true。

因为要统计第idx之前的所有糖果，则可以先初始化一个前缀和数组 `prefix` ， `prefix[i]` 表示第 `i` 个元素之前的元素之和。但要**注意本题的数据范围**，糖果数组 `candiesCount` 的长度和元素大小都是 $10^5$，所以元素之和会爆 `int` ，可以用 `unsigned long long` 来存前缀和。

- 最多天数：每天只吃一颗糖，最后一天刚好吃到第idx种糖的最后一颗，即需要 $prefix[i + 1]$ 天；
- 最少天数：每天都吃满 $cnt$颗糖，需要 $\lfloor \frac{prefix[i]}{cnt} \rfloor+1$ ；
  
  注意要向下取整，若第idx种糖果之前的糖果总和能被cnt整除，则刚好在第 $\frac{prefix[i]}{cnt} +1$ 天可以吃到第idx种糖果；若不能整除，在第$\lfloor \frac{prefix[i]}{cnt} \rfloor+1$天仍可以吃到第idx种糖果（吃完余数，再吃第idx种，保证当天总数不超过cnt）；
  
  且这里用除法虽然效率没乘法高（$(day-1) * cnt \ge prefix[i]$)，但不容易溢出，因为 $cnt$ 和 $day$ 的数据范围都是 $10^9$。

```cpp
typedef unsigned long long  ULL;

class Solution {
public:
    vector<ULL> prefix;
    bool canEat(const vector<int>& candiesCount, const vector<int>& query) {
        int idx = query[0], days = query[1] + 1, cnt = query[2];
        ULL latest_day = prefix[idx + 1];
        ULL earliest_day = prefix[idx] / cnt + 1;
        if(days >= earliest_day && days <= latest_day)
            return true;
        else 
            return false;

    }
    vector<bool> canEat(vector<int>& candiesCount, vector<vector<int>>& queries) {
        int n = candiesCount.size(), m = queries.size();
        prefix.resize(n + 1, 0);
        for(int i = 1; i <= n; i ++)
            prefix[i] = prefix[i - 1] + candiesCount[i - 1];
        
        vector<bool> ans(m, false);
        for(int i = 0; i < m; i ++)
            ans[i] = canEat(candiesCount, queries[i]);

        return ans;
    }
};
```

时间复杂度：计算前缀和是 $O(n)$，判断每个 query 是 $O(1)$，所有 queries 就是 $O(m)$，因此总的时间复杂度是 $O(n+m)$；

空间复杂度： $O(n)$ 

### 面经每日一题：MySQL的ACID，分别解释一下？