Datawhale每日一题打卡 week03 

实现语言：C++

## 06-01 day17
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

## 06-02 day18
### LC55. 跳跃游戏

[力扣](https://leetcode-cn.com/problems/jump-game/)

给定一个非负整数数组 `nums` ，你最初位于数组的 **第一个下标** 。数组中的每个元素代表你在该位置可以跳跃的最大长度。判断你是否能够到达最后一个下标。

**提示：**

- `1 <= nums.length <= 3 * 104`
- `0 <= nums[i] <= 105`

<div  align="center">  
<img src="https://gitee.com/nekomoon404/blog-img/raw/master/img/QQ图片20210602224244.png" width=50% />
</div>

【思路1——贪心】

对于位置 `i` ，在 `i` 处起跳能达到的最远位置就是 `i + nums[i]`；我们可以遍历数组，维护一个最远能到达的位置 `reach` ，枚举到当前元素 `i` 时，如果有 `reach < i` 则说明无论我们怎么跳都没办法跳到 `i` 处，也没无法跳到最后一个元素了；否则就更新一下 `reach = max(reach, i + nums[i]` 。这个过程可以看作是贪心的，在当前子问题中我们只关心当下的最优解 。

```cpp
bool canJump(vector<int>& nums) {
    int reach = 0, n = nums.size();
    for(int i = 0; i < n; i ++) {
        if(reach < i)
            return false;
        reach = max(reach, i + nums[i]);
    }
    return true;
}
```

上面的代码还可以做一下小优化：如果当前的 reach 跳的很远已经超过了 n - 1就可以提前返回了。

```cpp
bool canJump(vector<int>& nums) {
    int reach = 0, n = nums.size();
    for(int i = 0; i <= reach && reach < n - 1; i ++) 
        reach = max(reach, i + nums[i]);

    return reach >= n - 1;
}
```

时间复杂度： $O(n)$；时间复杂度： $O(1)$；

当然也可以倒着往回推，找到要到达终点需要的最早出发的位置，如果 `0` 位置为可以到达的状态，即返回 true。

```cpp
bool canJump(vector<int>& nums) {
    int last = n - 1, n = nums.size();
    for(int i = n - 2; i >= 0; i --) 
        if(i + nums[i] >= last)
	    last == i;

    return last == 0;
}
```

时间复杂度： $O(n)$；时间复杂度： $O(1)$；

### 面经每日一题：HTTP1.0, 1.1，2.0之间的区别

## 06-03 day 19

### LC56. 合并区间

[](https://leetcode-cn.com/problems/merge-intervals/)

以数组 `intervals` 表示若干个区间的集合，其中单个区间为 `intervals[i] = [starti, endi]` 。请你合并所有重叠的区间，并返回一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间。

提示：

- `1 <= intervals.length <= 10^4`
- `intervals[i].length == 2`
- `0 <= start_i <= end_i <= 10^4`

<div  align="center">  
<img src="https://gitee.com/nekomoon404/blog-img/raw/master/img/QQ图片20210603191225.png" width=50% />
</div>


【思路——贪心】

可以将所有区间按区间左端点从小到大排序，然后维护最大区间的左端点 `l` 和右端点 `r` ，遍历所有区间：

- 若当前区间的左端点小于等于 `r` ，则一定有重叠，此时更新 `r` 为 `r` 和 当前区间右端点的最大值；
- 若当前区间的左端点大于 `r` ，则维护的最大区间就到此为止了，与当前区间没有重叠，那就把 `{l, r}` 添加到答案数组中，然后更新 `l` 和 `r` 为当期区间的左右端点；遍历完后不要忘了把最后一个 `{l, r}` 加到答案中。

```cpp
vector<vector<int>> merge(vector<vector<int>>& intervals) {
    //sort(intervals.begin(), intervals.end(), [](auto const& a, auto const& b){
        //return a[0] < b[0];
    //});
    sort(intervals.begin(), intervals.end());

    vector<vector<int>> ans;
    int l = intervals[0][0], r = intervals[0][1];

    for(auto pair : intervals) {
        if(pair[0] <= r && pair[1] > r)
            r = pair[1];
        else if(pair[0] > r) {
            ans.push_back({l, r});
            l = pair[0], r = pair[1];
        }
    }

    ans.push_back({l, r});

    return ans;
}
```

时间复杂度：$O(n \log n)$，排序是 $O(n \log n)$，遍历是 $O(n)$，所以总体是 $O(n \log n)$；

空间复杂度：$O(1)$

### 面经每日一题：什么是僵尸进程？
