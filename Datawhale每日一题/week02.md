Datawhale每日一题打卡 week02——补卡 

实现语言：C++
## 05-17 day9
### LC3. 无重复字符的最长子串

## 05-19 day10
### LC409. 最长回文串

## 05-20 day11
### LC516. 最长回文子序列（求长度）

## 05-26 day12
### LC33. 搜索旋转排序数组

[力扣](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

一个升序数组，元素互不相同，在某个元素处旋转过后作为输入数组，查找数组中是否存在target。

【思路——二分查找】

和LC81.寻找旋转数组中的最小值类似，先用二分找分段点，但这一题去找第一段的右端点比较好，会省去处理一些边界条件；然后判断target在哪一段，去那一段二分查找第一个大于等于target的元素。

```cpp
int search(vector<int>& nums, int target) {
    if(nums.empty())  return -1;

    int l = 0, r = nums.size() - 1;
    while(l < r) {
        int mid = l + r + 1 >> 1;
        if(nums[mid] >= nums[0])  l = mid;
        else r = mid - 1;
    }
    //此时l = r = 第一段的最右端点，即整个数组的最大值
    if(target >= nums[0])  l = 0;
    else l = r + 1, r = nums.size() - 1;

    //在分段中找到第一个大于等于nums的数
    while(l < r) {
        int mid = l + r >> 1;
        if(nums[mid] >= target)  r = mid;
        else l = mid + 1;
    }

    //这里直接填nums[l] == target有些用例会通不过，要加一个判断 l < nums.size()
    //if(l < nums.size() && nums[l] == target)  return l;
    if(nums[r] == target)  return r;
    else return -1;
}
```

时间复杂度： $O(\log n)$；空间复杂度： $O(1)$

## 05-27 day13
### LC1143. 最长公共子序列

[力扣](https://leetcode-cn.com/problems/longest-common-subsequence/)

给定两个字符串 `text1` 和 `text2`，返回这两个字符串的最长 公共子序列 的长度。如果不存在 公共子序列 ，返回 0 。

一个字符串的 **子序列** 是指这样一个新的字符串：**它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串**。例如，"ace" 是 "abcde" 的子序列，但 "aec" 不是 "abcde" 的子序列。两个字符串的 公共子序列 是这两个字符串所共同拥有的子序列。

**提示：**

- `1 <= text1.length, text2.length <= 1000`
- `text1` 和 `text2` 仅由小写英文字符组成。

<div  align="center">  
<img src="https://gitee.com/nekomoon404/blog-img/raw/master/img/QQ图片20210601210957.png" width=50% />
</div>

【思路-LCS裸题】

最长公共子序列LCS是动态规划子序列问题中的一个经典的问题。可以从状态定义和状态转移入手来思考：

【状态定义】： `f[i][j]` 代表**考虑**s1的前 `i` 个元素，**考虑**s2的前 `j` 个元素，形成的最长公共子序列的长度

【状态转移】：状态定义中只说了【考虑前 `i` 个元素和考虑前 `j` 个元素】，并没有说【一定要包含第 `i` 个元素和第 `j` 个元素】，那我们这里来分情况讨论一下，一共要分四种情况：

1. 不包含 `s1[i]` 和 `s2[j]` ：可以用状态 `f[i-1][j-1]` 来表示；
2. 包含 `s1[i]` 和 `s2[j]` ：在 `s1[i] == s2[j]` 的前提下，可以用 `f[i-1][j-1] + 1` 来表示；
3. 不包含 `s1[i]` ，包含 `s2[j]` ：不能直接把这样情况表示出来，用 `f[i-1][j]` 表示必然不包含 `s1[i]` ，但可能包含 `s2[j]` 的情况，因此 `f[i-1][j]` 表示的是情况1和3的合集；

    但我们求的是【最大值】，只需要确保【不漏】即可保证答案的正确性，即某些情况被重复参与比较不影响正确性，因此这里直接用 `f[i-1][j]` 来表示也没有问题；

4. 包含 `s1[i]` ，但不包含 `s2[j]` ：与情况3同理，可以直接用 `f[i][j-1]` 来表示；

因此状态转移方程是：

$$f[i][j]= \begin{cases} \max(f[i-1][j], f[i][j-1], f[i-1][j-1]+1), & s1[i]=s2[j] \\ \max(f[i-1][j], f[i][j-1]), & s1[i] \ne s2[j] \end{cases}         $$

**可见LCS问题的状态转移是包含了【重复状态比较】的**。

在代码书写上，对于字符串的题，也可以在字符串前面加一个空格 `s1 = " " + s1` ，这样 `f[i][j]` 就对应了 `s1[i]` 和 `s2[j]` ；初始化时要将 `f[0][j] = 1, f[i][0] = 1` ，即将空格也算进去了，最后输出 `f[m][n] - 1` 。 

```cpp
int longestCommonSubsequence(string text1, string text2) {
    int m = s1.size(), n = s2.size();
    int f[m + 1][n + 1];
    memset(f, 0, sizeof f);

    for(int i = 1; i <= m; i ++) {
        for(int j = 1; j <= n; j ++) {
            if(s1[i - 1] == s2[j - 1])
                f[i][j] = f[i - 1][j - 1] + 1;
            else
                f[i][j] = max(f[i - 1][j], f[i][j - 1]);
        }
    }

    return f[m][n];
}
```

时间复杂度： $O(m*n)$；空间复杂度： $O(m*n)$，可以用滚动数组进行空间上的优化。

## 05-29 day14
### LC15. 三数之和
[力扣](https://leetcode-cn.com/problems/3sum/)

给你一个包含 n 个整数的数组 `nums`，判断 `nums` 中是否存在三个元素 `a，b，c` ，使得 `a + b + c = 0` ？请你找出所有满足条件且不重复的三元组。注意：**答案中不可以包含重复的三元组**。

提示：

* `0 <= nums.length <= 3000`
* `-105 <= nums[i] <= 10^5`

<div  align="center">  
<img src="https://gitee.com/nekomoon404/blog-img/raw/master/img/QQ图片20210601210656.png" width=50% />
</div>

这道题用哈希做的话比较麻烦，先两次遍历数组敲定第一个和第二个数，然后再用哈希去找第三个数，麻烦的地方在于元组的去重。

这道题考察的点应该还是在 **双指针**上，【先对数组进行排序，使用三个指针 `i, j, k` 分别代表要找的三个数】。

1. 遍历 `nums` 确定第一个数 `i` ，在其之后的子数组中，另外两个指针分别从左边 `i + 1` 和右边 `n - 1` 往中间移动，找到满足三数之和 `sum == 0` 的所有组合。
2. 指针 `j, k` 的移动逻辑，分情况讨论：
    - `sum > 0` ，则 `k --`  使和变小；
    - `sum < 0` ，则 `j ++` 使和变大；
    - `sum == 0` ，则将三个数存入答案。

关键是去重的部分，由于不能有重复的三元组，所在 **在确定第一个数和第二个数的时候，要跳过重复的数**（在三数之和的确定和情况下，确保第一个数和第二数不会重复，即可保证三元组不重复）

- 这道题本身的想法并不难，在代码的实现上要注意细节，为了使代码更简洁也有trick在里面。

【常规的写法，较好理解】：

```cpp
vector<vector<int>> threeSum(vector<int>& nums) {
    vector<vector<int>> res;
    int n = nums.size();
    sort(nums.begin(), nums.end());
    for(int i = 0; i < n; i ++) {
		//如果排序后的第一个数就大于0了，则说明无解
        if(nums[i] > 0)  return res;
		
		//错误的去重方法：nums[i] == nums[i + 1]，会漏掉如{-1, -1, 2}这种情况
        if(i > 0 && nums[i] == nums[i - 1])  continue;

        int j = i + 1, k = n - 1;
        while(j < k) {
            if(nums[i] + nums[j] + nums[k] < 0)  j ++;
            else if(nums[i] + nums[j] + nums[k] > 0)  k --;
            else {
                res.push_back(vector<int>{nums[i], nums[j], nums[k]});
                
                //找到答案后，再去重，否则会漏掉{0, 0, 0}这种情况；保证j < k，是要保证可以取到三个数
                while(j < k && nums[j] == nums[j + 1])  j ++;
                while(j < k && nums[k] == nums[k - 1])  k --;  //可以省略，前两个去重，第三数自然不会重

                //找到答案时，双指针同时收缩
                j ++, k --;
            } 
        }
    }
    return res;
}
```

代码简洁的写法：

```cpp
vector<vector<int>> threeSum(vector<int>& nums) {
    vector<vector<int>> res;
    int n = nums.size();
    sort(nums.begin(), nums.end());
    for(int i = 0; i < n; i ++) {
        if(i > 0 && nums[i] == nums[i - 1])  continue;   //第一个数去重
        for(int j = i + 1, k = n - 1; j < k; j ++) {
            if(j > i + 1 && nums[j] == nums[j - 1])  continue;   	//第二个数去重
				
			//这一句就比较巧妙了，结合例子看比较好理解
            while(k - 1 > j && nums[i] + nums[j] + nums[k - 1] >= 0)  k --;    	
				
            if(nums[i] + nums[j] + nums[k] == 0)
                res.push_back({nums[i], nums[j], nums[k]});
        }
    }
    return res;
}
```

时间复杂度：排序的复杂度为 $O(\log n)$，对于每个 `i` 而言，最坏的情况 `j` 和 `k` 都要扫描一遍数组的剩余部分，复杂度为 $O(n^2)$，所以整体的时间复杂度是 $O(n^2)$；

空间复杂度： $O(n^2)$

## 05-30 day15
### LC20. 有效的括号

[力扣](https://leetcode-cn.com/problems/valid-parentheses/)

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。

有效字符串需满足：

- 左括号必须用相同类型的右括号闭合。
- 左括号必须以正确的顺序闭合。

**提示：**

- `1 <= s.length <= 104`
- `s` 仅由括号 `'()[]{}'` 组成

<div  align="center">  
<img src="https://gitee.com/nekomoon404/blog-img/raw/master/img/QQ图片20210601204325.png" width=50% />
</div>

【思路——利用栈来解决匹配问题】

比较经典的括号匹配问题，利用栈先进后出的性质来解决匹配问题一直可以的🙂：

1. 遍历字符串，遇到左括号就把对应的右括号压入栈中；
2. 遇到与栈顶相同的右括号就弹出栈顶，即若遇到右括号发现栈非空且它与栈顶不同，则该括号序列一定是错误的；
3. 最后若栈是空的，说明序列合法。

```cpp
bool isValid(string s) {
    stack<char> st;
    for(char ch : s) {
        if(ch == '(')  st.push(')');
        else if(ch == '{')  st.push('}');
        else if(ch == '[')  st.push(']');
        else if(st.size() > 0 && ch == st.top())
            st.pop();
        else
            return false;
    }
    return st.size() == 0;
}
```

时间复杂度： $O(n)$；空间复杂度： $O(n)$

### 面经每日一题：Wide&Deep模型是怎样进行训练的，并详细说明其中使用到的FTRL算法。

## 05-31 day16
### LC22. 括号生成

[力扣](https://leetcode-cn.com/problems/generate-parentheses/)

数字 n 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 有效的 括号组合。

**提示：**

- `1 <= n <= 8`

<div  align="center">  
<img src="https://gitee.com/nekomoon404/blog-img/raw/master/img/QQ图片20210601204139.png" width=50% />
</div>


【思路1-全排列+判断结果合法】

比较直接的想法是先找出括号序列的全排列，考虑去重，可以参考LC47. 全排列II，然后判断找到的path是否合法，复杂度是$O(2n!*n) < O(2^{2n}*n)<2^{19}$，并不会超时。

判断括号序列合法常用的方法是借助栈，但本题中只有 `"()"` 一种括号，我们无需考虑括号匹配的问题，只需要关注括号的数量即可。下面的结论可以记住：

 **一个只有一种括号的括号序列合法的充要条件是：**

1. 任意前缀中左括号数量大于右括号数量；
2. 左括号数量等于右括号数量

如果只问由n对括号组成的合法括号序列的数量，**数量是第n个卡特兰数**  $\frac{C_{2n}^{n}}{n+1}$。

```cpp
class Solution {
public:
    vector<string> res;
    string path;
    bool isValid(const string& str){
        int balance = 0;
        for(char ch : str) {
            if(ch == '(')
                ++balance;
            else 
                --balance;
            if(balance < 0)
                return false;
        }
        return balance == 0;
    }

    void dfs(string& str, vector<bool>& used) {
        if(path.size() == str.size()) {
            if(isValid(path))
                res.push_back(path);
            return;
        }

        for(int i = 0; i < str.size(); i ++) {
            if(used[i] || i > 0 && str[i] == str[i - 1] && !used[i - 1])
                continue;
            
            used[i] = true;
            path.push_back(str[i]);

            dfs(str, used);

            path.pop_back();
            used[i] = false;

        }
    }

    vector<string> generateParenthesis(int n) {
        string str;
        for(int i = 0; i < n; i ++)
            str += "(";
        for(int i = 0; i < n; i ++)
            str += ")";
        
        vector<bool> used(n*2, false);
        dfs(str, used);

        return res;
    }
};
```

（有些笨的写法）

时间复杂度： $O(2n!*n)$；

空间复杂度： $O(n)$，除了要存答案数组的空间外，所需空间主要取决于递归调用栈的深度，递归调用栈的最大深度是 $2n$，每一层递归需要空间是 $O(1)$；

【思路2——回溯+剪枝】

进一步地，我们可以在生成括号序列的过程中进行剪枝，剪掉不合法的情况，而不是在最后的结果处判断是否合法（第一种方法的剪枝只是去掉排列中重复的情况）。

在DFS的过程中，我们可以只考虑当前位的下一位填什么，显然只有两种情况，填左括号和填右括号， 结合上面的括号序列合法的条件，下一位可以填左右括号需要的条件是：

1. 已使用左括号数量 < n，就可以填左括号；
2. 已使用右括号数量 < n，且左括号数量 > 右括号数量，就可以填右括号；

因此在DFS函数的签名中需要记录当前已使用的左右括号的数量，记为 `left` 和 `right` ，如果 `left < right` 就可以剪枝；

```cpp
class Solution {
public:
    vector<string> res;
    void dfs(string curStr, int left, int right, int n) {
        if(curStr.size() == n * 2 && left == right) {
            res.push_back(curStr);
            return;
        }

        if(left < right)
            return;
        
        if(left < n)
            dfs(curStr + '(', left + 1, right, n);
        if(right < n)
            dfs(curStr + ')', left, right + 1, n);
    }
    vector<string> generateParenthesis(int n) {
        dfs("", 0, 0, n);
        return res;
    }
};
```

时间复杂度：递归中一共会找到   $\frac{C_{2n}^{n}}{n+1}$ 种情况，每得到一次结果还会将字符串复制一次添加到答案数组中，是 $O(n)$，所以总的时间复杂度是 $O(C_{2n}^{n})$

空间复杂度： $O(n)$，同思路一。

回溯的Tips：上面的 `dfs(curStr + '(', left + 1, right, n);` 实际上是利用函数参数的性质来简化回溯的代码，把递归的过程写到递归函数里面，而不用去恢复现场，因为函数的执行并不会去改变curStr原本的数值，其实就等于下面的写法：

```cpp
curStr += '(';
dfs(curStr, left + 1, right, n);
curStr.pop_back();
```

### 面经每日一题：进程间通信方式IPC有哪些？说一下消息队列和共享内存的区别？