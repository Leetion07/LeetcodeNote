[toc]
## [剑指 Offer 03. 数组中重复的数字](https://leetcode.cn/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)
![image.png](https://note.youdao.com/yws/res/7080/WEBRESOURCE7085c14d71046aa334b11e41bb5cb161)
### 思路
原地哈希，由于数字范围为 [0, n - 1]，可以通过原数组建立哈希表，
```
index -> 是否存在(>0则不存在 <0 则存在)
```

```cpp
class Solution {
public:
    int findRepeatNumber(vector<int>& nums) {
        int n = nums.size();
        for (int i = 0; i < n; ++i) {
            if (nums[abs(nums[i])] < 0) {
                return abs(nums[i]);
            } else {
                nums[abs(nums[i])] = -abs(nums[abs(nums[i])]);
            }
        }
        return 0;
    }
};
```

## [剑指 Offer 04. 二维数组中的查找](https://leetcode.cn/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)
![image.png](https://note.youdao.com/yws/res/7092/WEBRESOURCEc16bc28a5f1809aa0baeb7e7b0df0635)
### 思路
观察可知右上角部分的外侧为单调序列.
可以根据对角线的值，去排除一行或是一列，进而缩小查找范围.
```
1 4 7 11 15
         19
         22
         24
         30
```

另外题解
![image.png](https://note.youdao.com/yws/res/7101/WEBRESOURCE9bae8adeec985f4c710d99abb3582779)

```cpp
class Solution {
public:
    bool findNumberIn2DArray(vector<vector<int>>& matrix, int target) {
        if (matrix.empty()) {
            return false;
        }
        int m = matrix.size(), n = matrix[0].size();
        int i = 0, j = n - 1;
        while (i <= m - 1 && j >= 0) {
            if (matrix[i][j] > target) {
                j -= 1;
            } else if (matrix[i][j] < target) {
                i += 1;
            } else {
                return true;
            }
        }
        return false;
    }
};
```

## [剑指 Offer 05. 替换空格](https://leetcode.cn/problems/ti-huan-kong-ge-lcof/)
![image.png](https://note.youdao.com/yws/res/7105/WEBRESOURCEcab4531757503d992fc4fb80f923a86d)
### 思路
1. 根据空格数量对数组进行扩容
2. 利用双指针法，从后向前进行遍历(若是正向遍历会覆盖后续信息).
- 若s[j] 不为空格，则直接赋值，指针++
- 若为空格，则赋值为 "%20"

```CPP
class Solution {
public:
    string replaceSpace(string s) {
        int n = s.size();
        int numSpace = 0;
        for (auto& ch: s) {
            if (isspace(ch)) {
                numSpace++;
            }
        }
        s.resize(n + numSpace * 2);
        int i = s.size() - 1, j = n - 1;
        while (j >= 0) {
            if (!isspace(s[j])) {
                s[i--] = s[j];
            } else {
                s[i--] = '0';
                s[i--] = '2';
                s[i--] = '%';
            }
            j--;
        }
        return s;
    }
};
```

## [剑指 Offer 06. 从尾到头打印链表](https://leetcode.cn/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/)
![image.png](https://note.youdao.com/yws/res/7115/WEBRESOURCE5448967f0fd8893d7e18c258101c1378)
### 思路
后序递归方法，可以打出逆序排列.

```CPP
class Solution {
public:
    vector<int> res;
    void traverse(ListNode* head) {
        if (head == nullptr) {
            return;
        }
        traverse(head->next);
        res.push_back(head->val);
    }
    vector<int> reversePrint(ListNode* head) {
        traverse(head);
        return res;
    }
};
```

## [剑指 Offer 07. 重建二叉树](https://leetcode.cn/problems/zhong-jian-er-cha-shu-lcof/)
![image.png](https://note.youdao.com/yws/res/7121/WEBRESOURCE83347cf2b491fb4594840c946c705c4a)
### 思路
1. 前序遍历的首位，为根结点，后序遍历末尾为根节点.
2. 根据根节点将中序遍历一分为2，确定出当前状态左右子树的大小，即可通过后序遍历进行构造.

```CPP
class Solution {
public:
    TreeNode* traverse(vector<int>& preorder, int lPre, int rPre
                     , vector<int>& inorder, int lIn, int rIn) {
        if (lPre > rPre) {
            return nullptr;
        }
        int root = preorder[lPre];
        int i = lIn;
        for (; i <= rIn; ++i) {
            if (root == inorder[i]) {
                break;
            }
        }
        int numL = i - lIn, numR = rIn - i;
        auto resL = traverse(preorder, lPre + 1, lPre + numL
                            ,inorder,       lIn, lIn + numL - 1);
        auto resR = traverse(preorder, lPre + numL + 1, rPre
                            ,inorder,  i + 1, rIn); 
        TreeNode* resM = new TreeNode(root);
        resM->left = resL;
        resM->right = resR;
        return resM;
    }

    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int n = preorder.size();
        if (n == 0) {
            return nullptr;
        }
        return traverse(preorder, 0, n - 1
                       ,inorder , 0, n - 1); 
    }
};
```
## [剑指 Offer 09. 用两个栈实现队列](https://leetcode.cn/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)
![image.png](https://note.youdao.com/yws/res/7131/WEBRESOURCEc12b815008536d919e0359948475ea34)
### 思路
将一个栈当作输入栈，用于压入appendTail传入的数据；另一个栈当作输出栈，用于deleteHead 操作。

每次 deleteHead时，若输出栈为空则将输入栈的全部数据依次弹出并压入输出栈，这样输出栈从栈顶往栈底的顺序就是队列从队首往队尾的顺序。
```CPP
class CQueue {
public:
    CQueue() {
    }
    void appendTail(int value) {
        stk1.push(value);
    }
    int deleteHead() {
        if (stk2.empty()) {
            if (stk1.empty()) {
                return -1;
            }
            while (!stk1.empty()) {
                stk2.push(stk1.top());
                stk1.pop();
            }
        }
        int rt = stk2.top();
        stk2.pop();
        return rt;
    }
private:
    stack<int> stk1;
    stack<int> stk2;
};
```

## [剑指 Offer 10- I. 斐波那契数列](https://leetcode.cn/problems/fei-bo-na-qi-shu-lie-lcof/)
![image.png](https://note.youdao.com/yws/res/7139/WEBRESOURCE9cf6446082c1a1390fad650eab92bb87)
### 思路
注意取模的位置，由于都是做的加减法，中途取模和最后取模对结果没有影响.
```CPP
class Solution {
public:
    int fib(int n) {
        int MOD = 1000000007;
        if (n == 0) {
            return 0;
        } 
        if (n == 1) {
            return 1;
        }
        int x1 = 0, x2 = 1;
        for (int i = 0; i <= n - 2; i++) {
            long tmp = x2;
            x2 = (x1 + x2)%MOD;
            x1 = tmp;
        }
        return x2;
    }
};
```

## [剑指 Offer 10- II. 青蛙跳台阶问题](https://leetcode.cn/problems/qing-wa-tiao-tai-jie-wen-ti-lcof/)
![image.png](https://note.youdao.com/yws/res/7146/WEBRESOURCE997c4dd296e36059ff87ff9d3d0b3d60)
### 思路
跳到第`i`级台阶，可以从`i-1`或`i-2`跳跃得来.

```CPP
class Solution {
public:
    int numWays(int n) {
        if (n == 0 || n == 1) return 1;
        int x1 = 1, x2 = 1;
        int MOD = 1e9 + 7;
        for (int i = 2; i <= n; ++i) {
            int tmp = x2;
            x2 = (x1 + x2) % MOD;
            x1 = tmp;
        }
        return x2;
    }
};
```
## [剑指 Offer 11. 旋转数组的最小数字](https://leetcode.cn/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/solution/)
![image.png](https://note.youdao.com/yws/res/7449/WEBRESOURCE45558d6fd6d364a63776d43bdc4aa305)
### 思路
![image.png](https://note.youdao.com/yws/res/7452/WEBRESOURCE8dc411c059c7a01856232bff2a50dd09)

```CPP
class Solution {
public:
    int minArray(vector<int>& numbers) {
        int n = numbers.size();
        int l = 0, r = n - 1, res = n - 1;
        while (l <= r) {
            int m = l + ((r - l) >> 1);
            if (numbers[m] > numbers[r]) {
                l = m + 1;
            } else if (numbers[m] < numbers[r]) {
                /// m 可能为最小值
                /// 比如说 3 1 2
                r = m;
            } else {
                ///
                r -= 1;
            }
        }
        return numbers[l];
    }
};
```

## [剑指 Offer 12. 矩阵中的路径](https://leetcode.cn/problems/ju-zhen-zhong-de-lu-jing-lcof/submissions/)
![image.png](https://note.youdao.com/yws/res/7467/WEBRESOURCE278b370f69a561ed6ef2ab79bc15afc7)
### 思路
深搜 + 回溯的方式.



```CPP
class Solution {
public:
    int m, n;
    vector<vector<int>> visted;
    vector<vector<int>> direction = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};
    bool isValid(int i, int j) {
        if (i >= 0 && i < m && j >= 0 && j < n && !visted[i][j]) {
            return true;
        }
        return false;
    }

    bool dfs(vector<vector<char>>& board, string word, int i, int j, int index) {
        if (index + 1 == word.size()) {
            return true;
        }
        visted[i][j] = true;
        // cout << i << ", " << j << ", " << index << endl;
        for (auto& d: direction) {
            int _i = i + d[0];
            int _j = j + d[1];
            if (isValid(_i, _j) && board[_i][_j] == word[index + 1]) {
                if (dfs(board, word, _i, _j, index + 1)) {
                    return true;
                }
            }
        }
        visted[i][j] = false;
        return false;
    }

    bool exist(vector<vector<char>>& board, string word) {
        m = board.size(); 
        n = board[0].size();
        visted.resize(m, vector<int>(n, 0));
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (board[i][j] == word[0] && dfs(board, word, i, j, 0)) {
                    return true;
                }
            }
        }
        return false;
    }
};
```

## [剑指 Offer 13. 机器人的运动范围](https://leetcode.cn/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/)
![image.png](https://note.youdao.com/yws/res/7680/WEBRESOURCE305e2bee955a4433368a2a128572ac0c)
### 思路
可以看作有障碍物的地图搜索问题，直接dfs或bfs即可.
**dfs**
```CPP
class Solution {
public:
    int res = 1;
    int maxM = 0, maxN = 0;
    vector<vector<int>> dir = {{0, 1}, {1, 0}};
    vector<vector<bool>> visited;
    
    bool isValid(int i, int j, int k) {
        int sum = 0;
        int _i = i, _j = j;
        while ((_i || _j)) {
            sum += _i % 10 + _j % 10;
            _i /= 10;
            _j /= 10;
        }
        return sum <= k && i < maxM && j < maxN && i >= 0 && j >= 0 && !visited[i][j];
    }

    void dfs(int i, int j, int k) {
        for (auto& d: dir) {
            int _i = i + d[0], _j = j + d[1];
            if (isValid(_i, _j, k)) {
                res++;
                // cout << _i << " " << _j << endl;
                visited[_i][_j] = true;
                dfs(_i, _j, k);
            }
        }
    }

    int movingCount(int m, int n, int k) {
        maxM = m;
        maxN = n;
        visited.resize(m, vector<bool>(n, false));
        visited[0][0] = true;
        dfs(0, 0, k);
        return res;
    }
};
```
**bfs**
```CPP
class Solution {
public:
    int res = 1;
    int maxM = 0, maxN = 0;
    vector<vector<int>> dir = {{0, 1}, {1, 0}};
    vector<vector<bool>> visited;
    
    bool isValid(int i, int j, int k) {
        int sum = 0;
        int _i = i, _j = j;
        while ((_i || _j)) {
            sum += _i % 10 + _j % 10;
            _i /= 10;
            _j /= 10;
        }
        return sum <= k && i < maxM && j < maxN && i >= 0 && j >= 0 && !visited[i][j];
    }

    int movingCount(int m, int n, int k) {
        if (!k) return 1;
        maxM = m;
        maxN = n;
        visited.resize(m, vector<bool>(n, false));
        visited[0][0] = true;
        stack<vector<int>> q;
        q.push({0, 0});
        while (!q.empty()) {
            int n = q.size();
            for (int i = 0; i < n; ++i) {
                auto xy = q.top();
                q.pop();
                for (auto& d: dir) {
                    int _x = xy[0] + d[0], _y = xy[1] + d[1];
                    if (isValid(_x, _y, k)) {
                        res++;
                        visited[_x][_y] = true;
                        q.push({_x, _y});
                    }
                }
            }
        }
        return res;
    }
};
```

## [剑指 Offer 14- I. 剪绳子](https://leetcode.cn/problems/jian-sheng-zi-lcof/)
![image.png](https://note.youdao.com/yws/res/7692/WEBRESOURCEd47a2184a9b9acbfaa6ec1f38cb01e20)
### 思路
题意为拆分整数使得乘积最大。记录`dp[i]`为 i 的最大拆分乘积结果.

对于`n`，可以比较`i * (n - i)` (i = 1,2...n - 1).

**dp状态定义**：记录`dp[i]`为 i 的最大拆分乘积结果.

**状态转移**：dp[n] = max(max(dp[n - i] * i, (n - i) * i), dp[n])

**初始化**：dp[2] = 1

**遍历顺序**：求`dp[2] -> dp[n]`,  判断哪一个拆分结果最大:
`{n - i, i}`. 其中 n - i 可拆分 也可以 不拆分 对应的分别为dp[n-i] 和 n - i.

```CPP
class Solution {
public:
    vector<int> res;
    void traverse(ListNode* head) {
        if (head == nullptr) {
            return;
        }
        traverse(head->next);
        res.push_back(head->val);
    }
    vector<int> reversePrint(ListNode* head) {
        traverse(head);
        return res;
    }
};
```
## [剑指 Offer 14- II. 剪绳子 II](https://leetcode.cn/problems/jian-sheng-zi-ii-lcof/)
![image.png](https://note.youdao.com/yws/res/8007/WEBRESOURCEf8eed8b8292bdbd60c1aa29548bca02a)
### 思路
根据数学推导可知，获取最大值的条件是尽可能拆分出3 > 2 > 1.
有 3 则拆为 3，否则就拆为 2.
![image.png](https://note.youdao.com/yws/res/8010/WEBRESOURCE161c11fbeaf16c07771e0b1ed3186822)
![image.png](https://note.youdao.com/yws/res/8015/WEBRESOURCE752843ddde5729c9a06348d7d2db5a55)
```CPP
class Solution {
public:
    long remainder(int x, int a, int p) {
        long rem = 1;
        for (int i = 0; i < a; i++) {
            rem = (rem * x) % p ;   
        }
        return rem;
    }
    int cuttingRope(int n) {
        if (n <= 3) return n - 1;
        int x = n % 3, y = n / 3, p = 1000000007;
        if (x == 0) {
            return remainder(3, y, p);
        } else if (x == 1) {
            return (4 * remainder(3, y - 1, p)) % p;
        } else {
            return (2 * remainder(3, y, p)) % p;
        }
    }
};
```

## [剑指 Offer 15. 二进制中1的个数](https://leetcode.cn/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/)
![image.png](https://note.youdao.com/yws/res/8019/WEBRESOURCE7a1fcf1c49b76beb29f8b22f674c29cb)
### 思路
**Brian Kernighan** 算法 统计 1 的个数。
观察这个运算：`n & (n − 1)`，其预算结果恰为把`n` 的二进制位中的最低位的 `1` 变为 `0` 之后的结果。
```CPP
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int cnt = 0;
        while (n != 0) {
            cnt++;
            n &= (n - 1);
        }
        return cnt;
    }
};
```
## [剑指 Offer 16. 数值的整数次方](https://leetcode.cn/problems/shu-zhi-de-zheng-shu-ci-fang-lcof/)
![image.png](https://note.youdao.com/yws/res/8031/WEBRESOURCEcee74c8bf5792359dcc2ee22d11656b0)
### 思路
```
2 ^ 3 = 2 ^ (0b0011)
= 2 ^ (0b0001 + 0b0010)
= 2 ^ (0b0001) * 2 ^ (0b0010)
= 2 * (2 * 2) 


```

```CPP
class Solution {
public:
    double myPow(double x, int n) {
        bool flag = false;
        long long N = n;
        if (N < 0) {
            N = abs(N);
            flag = true;
        }
        double res = 1;
        while (N != 0) {
            res *= (N & 1)? x: 1;             
            x *= x;
            N >>= 1;
        }
        return flag? 1 / res: res;
    }
};
```

## [剑指 Offer 17. 打印从1到最大的n位数](https://leetcode.cn/problems/da-yin-cong-1dao-zui-da-de-nwei-shu-lcof/)
![image.png](https://note.youdao.com/yws/res/8077/WEBRESOURCE873c42181704adfb05650e2db646849c)
### 思路
本体考察的应该是大数的情况，因此采用`string`作为输出
回溯的思路：
从最高位开始回溯，每层递归回溯进行`for 循环 0 -> 9`
排除高位为`0`的情况.
采集路径结果时，需要考虑为空的情况.

```CPP
class Solution {
public:
    vector<int> res;
    string path;
    int maxIndex;
    void dfs(int startIndex) {
        if (startIndex == maxIndex) {
            if (!path.empty())
                res.emplace_back(stoi(path));
            return;
        }
        for (int i = 0; i <= 9; ++i) {
            if (i == 0 && path.empty()) {
                dfs(startIndex + 1);
            } else {
                path.push_back('0' + i);
                dfs(startIndex + 1);
                path.pop_back();  
            }
        }
    }

    vector<int> printNumbers(int n) {
        maxIndex = n;
        dfs(0);
        return res;
    }
};
```

## [剑指 Offer 18. 删除链表的节点](https://leetcode.cn/problems/shan-chu-lian-biao-de-jie-dian-lcof/)
### 思路
本体考察的应该是大数的情况，因此采用`string`作为输出
回溯的思路：
从最高位开始回溯，每层递归回溯进行`for 循环 0 -> 9`
排除高位为`0`的情况.
采集路径结果时，需要考虑为空的情况.

```CPP
class Solution {
public:
    vector<int> res;
    string path;
    int maxIndex;
    void dfs(int startIndex) {
        if (startIndex == maxIndex) {
            if (!path.empty())
                res.emplace_back(stoi(path));
            return;
        }
        for (int i = 0; i <= 9; ++i) {
            if (i == 0 && path.empty()) {
                dfs(startIndex + 1);
            } else {
                path.push_back('0' + i);
                dfs(startIndex + 1);
                path.pop_back();  
            }
        }
    }

    vector<int> printNumbers(int n) {
        maxIndex = n;
        dfs(0);
        return res;
    }
};
```

