[TOC]

栈的定义
---
![image.png](https://note.youdao.com/yws/res/1456/WEBRESOURCE736aa0a664cd4a0ef08e8b002faa0e60)
栈（stack）是一种线性数据结构，它就像一个上图所示的放入乒乓球的圆筒容器。最早进入的元素存放的位置叫作栈底 （bottom），最后进入的元素 存放的位置叫作栈顶 （top）。

单调栈
---
单调栈是指一个栈内部的元素具有严格单调性的一种数据结构，分为单调递增栈和单调递减栈。

其具有以下两个性质：

1，满足栈底到栈顶的元素具有严格单调性。

2，满足栈的先进后出特性，越靠近栈顶的元素越后出栈。
![image.png](https://note.youdao.com/yws/res/1461/WEBRESOURCEf5f2a445e2343b0f93f4e3a21a25e539)
元素进栈过程：

对于一个单调递增栈来说，若当前进栈的元素为a，如果a<栈顶元素，则直接将a进栈。

如果a≥栈顶元素，则不断将栈顶元素出栈，直到满足a<栈顶元素。

---

单调队列
---
队列中元素之间的关系具有单调性，而且，队首和队尾都可以进行出队操作，只有队尾可以进行入队操作。

其具有以下两个性质：

- 队列中的元素其对应在原来的序列中的顺序必须是单调递增的。
- 队列中元素的大小必须是单调递(增/减/自定义)。

```cpp
#include <bits/stdc++.h>
using namespace std;
class monotoneQueue{
private:
    deque<int> q;
public:
    monotoneQueue() {

    }
    int front() {
        return q.front();
    }
    
    void push(int val) {
        /*单调递减队列*/
        while (!q.empty() && q.back() < val) {
            q.pop_back();
        }
        q.push_back(val);
    }

    void pop(int val) {
        if (q.front() == val) {
            q.pop_front();
        }
    }
};
```


## [42. 接雨水](https://leetcode-cn.com/problems/trapping-rain-water/)
### **思路**
维护一个递增单调栈。当遇到退栈时：
比如说：5 3 2 1 0 **2**
插入一个2，则计算下凹槽的体积。需要的三个元素，1 0 2。待进栈的元素和下个待出栈元素构成了两边，计算该层的体积即可。
top[i-1], top[i], height[i]

==ps：该题需要的左右第一个最大值，则考虑递增栈，因为递增栈有出栈的情况时，恰好左右元素刚好满足需求==

**栈顶**和**栈顶的下一个元素**以及要**入栈**的三个元素来接水！

那么雨水高度是 min(凹槽左边高度, 凹槽右边高度) - 凹槽底部高度，代码为：int h = min(height[st.top()], height[i]) - height[mid];

雨水的宽度是 凹槽右边的下标 - 凹槽左边的下标 - 1（因为只求中间宽度），代码为：int w = i - st.top() - 1 ;

当前凹槽雨水的体积就是：h * w。
特殊情况: 相等时候的处理，如果当前遍历的元素（柱子）高度等于栈顶元素的高度，要跟更新栈顶元素，因为遇到相同高度的柱子，需要使用最右边的柱子来计算宽度。
![image.png](https://note.youdao.com/yws/res/1552/WEBRESOURCEefec1796704b4a5fa3515b0b98a829fd)
```cpp
class Solution {
public:
    int trap(vector<int>& height) {
        int res = 0;
        stack<int> stk;
        stk.push(0);
        for (int i = 1; i < height.size(); i++) {
            if (height[stk.top()] > height[i]) {
                stk.push(i);
            } else if (height[stk.top()] == height[i]) {
                stk.pop();
                stk.push(i);
            } else {
                while (!stk.empty() && height[stk.top()] < height[i]) {
                    int mid = stk.top();
                    stk.pop();
                    if (!stk.empty()) {
                        int h = min(height[stk.top()], height[i]) - height[mid];
                        int w = i - stk.top() - 1;
                        res += h * w;
                    }
                }
                stk.push(i);
            }
        }
        return res;
    }
};
```
## [84. 柱状图中最大的矩形](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)
### **思路**
其实就是对于每一个高度，进行画最大的矩形，比较每一个高度哪一个要大一点。
比如说2 1 5 6 2 3
2高度：最高就是2
1高度：最高就是1 1 1 1 1 1
...
5高度：5 5
入手点是：要找到每一列对应的左边第一个较小的数的索引以及每一列右边第一个较小数的索引
![image.png](https://note.youdao.com/yws/res/1663/WEBRESOURCE367f6531edfa2a53fed1d9a9e9a197eb)

==由于是两边的较小值，则考虑用递减栈，可以出现两边为首次出现的最小值的情况==
首末的结果算不出来怎么办？
首部加上0，确保第一个元素的前面有值，构成三连。
尾部加上0，确保最后一个元素必定弹出栈(尾部若为最小值的话)，构成三连
```cpp
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        stack<int> st;
        heights.insert(heights.begin(), 0); // 数组头部加入元素0
        heights.push_back(0); // 数组尾部加入元素0
        st.push(0);
        int result = 0;
        for (int i = 1; i < heights.size(); i++) {
            if (heights[i] > heights[st.top()]) {
                st.push(i);
            } else if (heights[i] == heights[st.top()]) {
                st.pop(); // 这个可以加，可以不加，效果一样，思路不同
                st.push(i);
            } else {
                while (!st.empty() && heights[i] < heights[st.top()]) { // 注意是while
                    int mid = st.top();
                    st.pop();
                    int left = st.top();
                    int right = i;
                    int w = right - left - 1;
                    int h = heights[mid];
                    result = max(result, w * h);
                }
                st.push(i);
            }
        }
        return result;
    }
};

```
## [239. 滑动窗口的最大值]()
### **思路**
引出:
构造一个单调队列。[入队元素的以最大值开头的单调递减部分（不改变相对顺序）]
如：1 3 -1 -3 中的 3 -1 -3
    1 3 -1 -3 2中的 3 2
单调队列的性质：
1. push(value)：如果push的元素value大于入口元素的数值，那么就将队列入口的元素弹出，直到push元素的数值小于等于队列入口元素的数值为止(理解：加入进来的数值时，为了尽可能少的操作下进行排序，**移位是最不可取的**，==由于没必要保留小于当前新加入进来的数值的已加入的数值，则先pop，把新的加入尾部是最高效的方式==)
2. pop(value)：如果窗口移除的元素value等于单调队列的出口元素，那么队列弹出元素，否则不用任何操作(理解：鉴于push操作，只需要判断是否等于当前出口元素，若小于，则已经在push该出口元素时被弹出了)
3. 总的来说就是把pop的部分操作分给了push部分。整体还是对各个元素操作了两次：一进一出。
```cpp
class Solution {
private:
    class MyQueue { //单调队列（从大到小）
    public:
        deque<int> que; // 使用deque来实现单调队列
        // 每次弹出的时候，比较当前要弹出的数值是否等于队列出口元素的数值，如果相等则弹出。
        // 同时pop之前判断队列当前是否为空。
        void pop(int value) {
            if (!que.empty() && value == que.front()) {
                que.pop_front();
            }
        }
        // 如果push的数值大于入口元素的数值，那么就将队列后端的数值弹出，直到push的数值小于等于队列入口元素的数值为止。
        // 这样就保持了队列里的数值是单调从大到小的了。
        void push(int value) {
            while (!que.empty() && value > que.back()) {
                que.pop_back();
            }
            que.push_back(value);

        }
        // 查询当前队列里的最大值 直接返回队列前端也就是front就可以了。
        int front() {
            return que.front();
        }
    };
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        MyQueue que;
        vector<int> result;
        for (int i = 0; i < k; i++) { // 先将前k的元素放进队列
            que.push(nums[i]);
        }
        result.push_back(que.front()); // result 记录前k的元素的最大值
        for (int i = k; i < nums.size(); i++) {
            que.pop(nums[i - k]); // 滑动窗口移除最前面元素
            que.push(nums[i]); // 滑动窗口前加入最后面的元素
            result.push_back(que.front()); // 记录对应的最大值
        }
        return result;
    }
};
```
## [496. 下一个更大元素 I](https://leetcode-cn.com/problems/next-greater-element-i/)
### 思路
注意题目： nums1是nums2的子集，**单调栈弹栈时，就说明了第一次找到比它大的数了。**
利用hashmap做nums1的val->index的映射
单调栈去找首次出现的最大值。
```cpp
class Solution {
public:
    vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {
        stack<int> stk;
        vector<int> res(nums1.size(), -1);
        unordered_map<int, int> umap;
        for (int i = 0; i < nums1.size(); i++) {
            umap[nums1[i]] = i;
        }
        stk.push(0);
        for (int i = 1; i < nums2.size(); i++) {
            while (!stk.empty() && nums2[stk.top()] < nums2[i]) {
                if (umap.find(nums2[stk.top()]) != umap.end()) {
                    res[umap[nums2[stk.top()]]] = nums2[i];
                }
                stk.pop();
            }
            stk.push(i);
        } 
        return res;
    }
};
```
## [503.下一个更大元素II](https://leetcode-cn.com/problems/next-greater-element-ii/)
### **思路**
单调递增栈
和每日温度类似，只需要循环2*size次即可。
```cpp
class Solution {
public:
    vector<int> nextGreaterElements(vector<int>& nums) {
        vector<int> result(nums.size(), -1);
        if (nums.size() == 0) return result;
        stack<int> st;
        for (int i = 0; i < nums.size() * 2; i++) {
            // 模拟遍历两边nums，注意一下都是用i % nums.size()来操作
            while (!st.empty() && nums[i % nums.size()] > nums[st.top()]) {
                result[st.top()] = nums[i % nums.size()];
                st.pop();
            }
            st.push(i % nums.size());
        }
        return result;
    }
};
```

## [739. 每日温度](https://leetcode-cn.com/problems/daily-temperatures/)
### **思路**
单调栈用在什么时候：
**通常是一维数组，要寻找任一个元素的右边或者左边第一个比自己大或者小的元素的位置，此时我们就要想到可以用单调栈了。**
利用单调递增栈。当压栈时，需要判断是否入栈元素比栈顶元素大，若是，则弹出栈元素直到从栈顶到栈底呈现单调递增。记录是在每个元素弹出时候记录，**弹出时候就代表了此时是第一次遇到比该元素大的值，它才回弹出去。**
```cpp
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& T) {
        stack<int> st; 
        vector<int> result(T.size(), 0);
        for (int i = 0; i < T.size(); i++) {
            while (!st.empty() && T[i] > T[st.top()]) { // 注意栈不能为空
                result[st.top()] = i - st.top();
                st.pop();
            }
            st.push(i);
        }
        return result;
    }
};
```