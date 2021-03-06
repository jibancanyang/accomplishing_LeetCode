Leetcode Answer.
========
The way is not so far.It has 337 problems until 2016-03-15.
Go on!my friends.

#LeetCode 通关之路

## 目录
* [Problem 1 to 10](#P1)
* [Problem 11 to 20](#P2)
* [Problem 21 to 30](#P6)
* [Problem 31 to 40](#P4)
* [Problem 41 to 50](#P5)
* [Problem 51 to 60](#P3)


<h1 id="P1">P1</h1>


***
##1.Two Sum(水)
**分析**:数学里的$\binom{2}{n}$.
**code**:
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        for (int i = 0; i < nums.size(); i++) {
            for (int j = i + 1; j < nums.size(); j++) {
                if (nums[i] + nums[j] == target) {
                    vector<int> ret;
                    ret.push_back(i);
                    ret.push_back(j);
                    return ret;
                }
            }
        }
    }
};
```
***
##2.Add Two Numbers(水)
**分析**大数加法,链表实现.
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* ret = new ListNode(0);
        ListNode* cur = ret;
        int op = 0;
        bool c1 = true, c2 = true;
        while (true) {
            int a = 0, b = 0;
            if (c1) {
                a = l1 -> val;
                if (l1 -> next == NULL) c1 = false;
            }
            if (c2) {
                b = l2 -> val;
                if (l2 -> next == NULL) c2 = false;
            }
            cur -> val = (op + a + b) % 10;
            if (op + a + b > 9) op = 1;
            else op = 0;
            cur -> next = new ListNode(0);
            
            if (!c1 && !c2) {
                if (op) {
                    cur = cur -> next;
                    cur -> val = op;
                } else cur -> next = NULL;
                return ret;
            }
            cur = cur -> next;
            if (l1 -> next != NULL) l1 = l1 -> next;
            if (l2 -> next != NULL) l2 = l2 -> next;
        }
    }
};
```
***
##3.Longest Substring Without Repeating Characters(尺取法(双指针))
**题意**:求字符串最长不重复字符子序列的长度.
**分析**:
> **暴力**:最直接的方法就是通过枚举所有的头和尾把所有的子串找出来,然后判断这个子串是不是满足其中字符全部不重复.这样的时间复杂度是$O(n ^ 3)$,显然效率极低.
> **尺取法(双指针)** :维护一个双指针,保证头尾之间的序列一定是不重复的,用一个letters表来记录是否重复.不熟悉尺取法(又名双指针)的话会比较困难.该算法时间复杂度$O(n)$,是最佳时间复杂度.

**尺取法的代码**:
```cpp

class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        bool letters[260];
        memset(letters, 0, sizeof(letters));
        int head = 0, tail = 0, maxlen = 0;
        letters[s[head]] = true;
        while (tail < s.size()) {
            if (head < tail && letters[s[tail] ]) {
                letters[s[head++]] = false;
                while (s[head - 1] != s[tail]) letters[s[head++]] = false;
            }
            letters[s[tail]] = true;
            maxlen = max(tail - head + 1, maxlen);
            tail++;
        }
        return maxlen;
    }
};
```
***
##4.Median of Two Sorted Arrays(递归截断)
**题意**:给两个已经排序的数组,求它们合并后的中位数,要求复杂度是$O(log(n + m))$.
**分析**:
- 首先很容易想到利用归并排序合并两个数组的方法来合并这两个有序数组,时间复杂度是$O(n)$
- 然后继续想可以以第一个数组为基准,来进行二分.一旦第一个数组中的元素选定,由于中位数就是中间的数字,那么第二个数组中数的对应位置也就确定了.二分第一个数组的位置,然后判断第一个第二个数组的选择部分是否满足条件.这样实现了一晚上,失败..细节太多.
- 然后就是log(n + m) 的正确解答.利用了一个重要的性质.**如果我们要在两个有序数组中选择第$k$个数,那么取第一个数组的第$k / 2$个数,和第二个数组的第$k - k / 2$个数,如果第一个数组的$k / a$个数小于第二个数组的$k - k/ a$个数,那么第一个数组的前$k / 2$个数中一定没有目标中位数,直接截断,第二个数组小的话也同理**这样就是形成了一个每次让其中一个数组长度截断一半的递归...

> 注意的是一定要保证思路完全清晰了再开始写代码,然后尽量通读代码查错而不是靠单步调试...
> 思考过程就是列出所有可能具有的性质,然后来尽量让这些性质交织着向正确答案靠近.

**code**:
```

class Solution {
public:
    double doit(int a, int b, int t, vector<int> &v1, vector<int> &v2) {
        int ka = min((int)v1.size() - a, t / 2), kb = t - ka;
        //The first is always shorter.
        if (v1.size() - a > v2.size() - b) return doit(b, a, t, v2, v1);
        if (v1.size() == a) return v2[b + t - 1];
        
        //When only choose one number.
        if (t == 1) return double(min(v1[a], v2[b]));
        
        if (v1[a + ka - 1] == v2[b + kb - 1]) return double(v1[a + ka - 1]);
        else if (v1[a + ka - 1] < v2[b + kb - 1]) return doit(a + ka, b, t - ka, v1, v2);
        else return doit(a, b + kb, t - kb, v1, v2);
    }
    
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        if ((nums1.size() + nums2.size()) % 2) return doit(0, 0, (int)(nums1.size() + nums2.size() + 1) / 2, nums1, nums2);
        else return (doit(0, 0, (int)(nums1.size() + nums2.size() + 1) / 2, nums1, nums2) +
            doit(0, 0, (int)(nums1.size() + nums2.size() + 2) / 2, nums1, nums2)) / 2;
    }
};
```
***
##5.Longest Palindromic Substring(最长回文子串)
**题意**:求字符串的最长回问子串.
**分析**:
- 首先很容易想到暴力的方法,把回文子串分成奇数长度和偶数长度两种情况,把每个字符当做回文串的中心,然后求以它为奇数串中心(或者偶数串的左中心),求它为中心的最长回文串的长度.这样复杂度是$O(n ^2)$,LeetCode也能AC....

**暴力的代码:**
```
class Solution {
public:
    string longestPalindrome(string s) {
        int retodd = 0, reteven = 0, odd = 0, even = 0;
        for (int i = 0; i < s.size(); i++) {
            for (int l = i, r = i; l >= 0 && r < s.size(); l--, r++) {
                if (s[l] != s[r]) break;
                if (r - l + 1 > retodd) {
                    retodd = r - l + 1;
                    odd = i;
                } 
            }
            
            for (int l = i, r = i + 1; l >= 0 && r < s.size(); l--, r++) {
                if (s[l] != s[r]) break;
            
                if (r - l + 1 > reteven) {
                    reteven = r - l + 1;
                    even = i;
                } 
            }
        }
        if (retodd > reteven) {
            return s.substr(odd - retodd / 2, retodd);
        } else {
            return s.substr(even - reteven / 2 + 1, reteven);
        }
    }
};
```
**Manacher算法**:强悍的马拉车算法,时间复杂度$O(n)$...
```cpp
class Solution {
public:
    string preProcess(string s)
    {
        int n = s.length();
        if (n == 0) return "^$";
        string ret = "^";
        for (int i = 0; i < n; i++)
            ret += "#" + s.substr(i, 1);
    
        ret += "#$";
        return ret;
    }
    
    string longestpalindrome(string s)
    {
        string T = preProcess(s);
        int n = T.length();
        int* P = new int[n];
        int C = 0, R = 0;
        for (int i = 1; i < n - 1; i++) {
            int i_mirror = 2 * C - i; // 等于i' = C - (i-C)
    
        P[i] = (R > i) ? min(R-i, P[i_mirror]) : 0;
    
        // 尝试扩展中心为i的回文
        while (T[i + 1 + P[i]] == T[i - 1 - P[i]])
            P[i]++;
    
        // 如果中心为i的回文超越了R，根据已扩展的回文来调整中心
        if (i + P[i] > R) {
            C = i;
            R = i + P[i];
            }
        }
    
        // 找出P中的最大值
        int maxLen = 0;
        int centerIndex = 0;
        for (int i = 1; i < n-1; i++) {
            if (P[i] > maxLen) {
                maxLen = P[i];
                centerIndex = i;
            }
        }
        delete[] P;
    
        return s.substr((centerIndex - 1 - maxLen)/2, maxLen);
    }
    string longestPalindrome(string s) {
        return longestpalindrome(s);
    }
};
```

***
6.ZigZag Conversion(模拟)
分析:把一个Z字型的字符,按行输出,模拟一下就好...复杂度$O(n)$
```cpp
class Solution {
public:
    string convert(string s, int numRows) {
        if (numRows == 1) return s;
        int key = 2 * numRows - 2;
        string ans[10000];
        for (int i = 0; i < s.size(); i += key) {
            int l = i, k = i + key - 1;
            ans[l % key] += s[l];
            l++;
            while (l < k) {
                if (l < s.size()) ans[l % key] += s[l];
                if (k < s.size()) ans[l % key] += s[k];
                l++, k--;
            }
            if (l < s.size()) ans[l % key] += s[l];
        }
        string ret;
        for (int i = 0; i < key; i++) {
             if (!ans[i].empty()) ret += ans[i];
        }
        return ret;
    }
};
```
***
##7.Reverse Integer(模拟)
**题意**:翻转一个int型数,注意坑点是数据范围超过会出现问题.总是很草率的提交,写完了得检查一下逻辑,争取一次AC率高一些啊...
> **一些语言特性**:
> `#include <climits>`库里面有一些表示数据范围的常量:
> `INT_MAX` `INT_MIN` `LONG_MAX` `CHAR_MAX` ....诸如此类.

**本题代码**:
```
#include <climits>
class Solution {
public:
    long long reverse(int x) {
        string str = to_string(x);
        for (int i = (str[0] == '-' ? 1 : 0), j = (int)str.size() - 1; i < j; i++, j--) {
            swap(str[i], str[j]);
        }
        long long temp = 0;
//        cout << str << endl;
        for (int i = (str[0] == '-' ? 1 : 0); i < str.size(); i++) {
//            cout << temp << endl;
            temp = temp * 10 + str[i] - '0';
        }
        if (temp > INT_MAX) return 0;
        return str[0] == '-' ? -temp : temp;
    }
};
```

***

##8. String to Integer (atoi)(C++)
可以说是一道恶心的模拟题,但是恰好C++库里有可以直接拿来用的stringtoint的标准函数.
```
class Solution {
public:
    int myAtoi(string str) {
        if (str == "") return 0;
        istringstream ret(str); //定义一个输入字符串流
        int i;
        ret >> i; //把一个输入流中的内容输出为整型
        return i;
    }
};
```
***
##9.Palindrome Number(模拟)
**题意**:要求不用额外空间判断是否是回文数.
这里我用了一个int变量,根据(/10, %10)组合操作来取它所有的数.
```cpp
class Solution {
public:
    bool isPalindrome(int x) {
        if (x < 0 || (x != 0 && x % 10 == 0)) return false;
        int y = 0;
        while (y < x) {
            y = y * 10 + x % 10;
            x /= 10;
        }
        return x == y || x == y / 10;
    }
};


```

***
##10. Regular Expression Matching （动态规划)
$dp[i][j]$表示匹配到s的i和p的j处。主要是考虑p为'*'的时候其前面的字符可以表示从0到任意次时如何处理。

```
class Solution {
public:
    bool dp[1234][1234];
    
    bool isMatch(string s, string p) {
        memset(dp, false, sizeof(dp));
        dp[0][0] = true;
        s = '^' + s, p = '$' + p;
        for (int i = 1; i <= p.size(); ++i) {
            if (p[i] == '*' && dp[0][i - 2]) dp[0][i] = true;
        }
        for (int i = 1; i <= s.size(); ++i) {
            for (int j = 1; j <= p.size(); ++j) {
                if ( (s[i] == p[j] || p[j] == '.') && dp[i - 1][j - 1]) dp[i][j] = true;
                if (p[j] == '*') {
                    if (dp[i][j - 2] || dp[i][j - 1] || ( (s[i] == p[j - 1] || p[j - 1] == '.') && (dp[i - 1][j - 1]
                    || dp[i - 1][j]) ) ) dp[i][j] = true;
                }
            }
        }
        return dp[s.size()][p.size()];
    }
};
```

<h1 id="P2">P2</h1>
======

***
##11. Container With Most Water(双指针)
经过思考之后，我发现这个题有复杂度为$O(n)$的优秀方法。
我们考虑来逐渐淘汰掉两边线段的思路：首先设置两个指针`head`和`tail`，一个指向第$0$号线段，一个指向第$n-1$号线段：
![Alt text](./屏幕快照 2016-07-01 下午7.37.16.png)
然后设置一个初始值为$0$的最大容量$maxV$。
1. 这个时候两个指针指向的线段形成的容器的容量是：$$V = (tail - head) * min\{a_{head}, a_{tail}\}$$
用这个$V$，去更新最大容量变量$maxV$
2. 如果此时$a_{head}<a_{tail}$，淘汰掉当前head指向的线段，head指针右移动一格。
3.  如果此时$a_{head}>=a_{tail}$，淘汰掉当前tail指向的线段，tail指针左移动一格。
4.  重复执行1,2,3步骤，直到$head$指针和$tail$指针相撞，所有线段被淘汰到只剩下最后一根了，没法形成容器。

可以看出两个指针加起来最多移动$n$次，所以这个算法的时间复杂度是$O(n)$。

为什么我们把较小的那一边向中间靠拢的策略是正确的呢？
假设当前局面如图:
![Alt text](./屏幕快照 2016-07-01 下午7.55.10.png)
按我给出的算法，我就会把$head$右移动一格，也就是$head$这根线段被淘汰了。我们把这时候的坐标轴划分为A,B,C共$3$个区域:
![Alt text](./屏幕快照 2016-07-01 下午7.59.58.png)

A，C区的线段已经被我们淘汰了，不能参与了，所以直接忽略。
对于B区无论有多少高度的线段，都不会和$x$线段形成比$x$和$y$形成的容器容量更大的容器，因为$x$和B区的线段形成的容器高度最大不过$x$的高度$a_x$，而长度也会小于$y-x$,所以是不能更大容量的。
所以我们可以直接把$x$线段淘汰，它不会再有用了。
这个逐步淘汰两边线段的算法是否惊艳到你了？
最后给出`C++`代码：
```
class Solution {
public:
    int maxArea(vector<int>& height) {
	    //设置头尾指针
        int head = 0, tail = height.size() - 1;
        //设置最大容量变量
        int maxv = 0;
        while (head < tail) { //退出条件是头尾指针相撞
            //最大容量更新
            maxv = max(maxv, min(height[head], height[tail]) * (tail - head));
            //谁更短就淘汰谁
            if (height[head] < height[tail]) {
                head++;
            } else {
                tail--;
            }
        }
        return maxv;
    }
};
```

***
## 14. Longest Common Prefix (模拟)
有两种扩展方法都很简单，最坏复杂度都是$O(ln)$

```
class Solution(object):
    def longestCommonPrefix(self, strs):
        """
        :type strs: List[str]
        :rtype: str
        """
        if len(strs) == 0: 
            return ""
        ret = ""
        first = strs[0]
        flag = False
        for i in range(0, len(strs[0])):
            for str in strs:
                if len(str) == i or str[i] != first[i]:
                    flag = True
            if flag:
                break
            ret += first[i]
            
        return ret
```

***
## 15. 3Sum
固定头部，双指针尾巴和中部，ksum问题都是枚举前面的维度，最后维度用双指针
```
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int> > ans;
        if (nums.size() < 3) return ans;
        sort(nums.begin(), nums.end());
        for (int head = 0; head < nums.size() - 2; ++head) {
            if (head > 0 && nums[head] == nums[head - 1]) continue;
            int p = head + 1;
            for (int tail = nums.size() - 1; tail > head + 1 && p < tail; --tail) {
                if (tail < nums.size() - 1 && nums[tail] == nums[tail + 1]) continue;
                while (p < tail && nums[head] + nums[p] + nums[tail] < 0) ++p;
                if (p < tail && nums[head] + nums[p] + nums[tail] == 0 && !(p > head + 1 && nums[p] == nums[p - 1])) {
                    vector<int> t = vector<int>({nums[head], nums[p], nums[tail]});
                    ans.push_back(t);
                }
            }
        }
        return ans;
    }
};
```

## 19. Remove Nth Node From End of List
由于是倒数第多少个，所以至少要先知道链表长度。知道长度之后，最快的方法就是把经过的节点存到数组里，直接索引到该节点。

```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode *now = head;
        vector<ListNode*> nodes;
        while (true) {
            nodes.push_back(now);
            if (now -> next == NULL) break;
            now = now -> next;
        }
        ListNode *lst, *nxt;
        if (nodes.size() - n == 0) lst = NULL;
        else lst = nodes[nodes.size() - n - 1];
        if (n == 1) nxt = NULL;
        else nxt = nodes[nodes.size() - n + 1];
        if (nxt && lst) lst -> next = nxt;
        else if (nxt) head = nxt;
        else if (lst) lst -> next = NULL;
        else return NULL;
        return head;
    }
};
```
<h1 id="P6">P6</h1>

## 22. Generate Parentheses
从左到右生成括号串，核心就是括号串满足的条件，左到右遍历，每一个时刻右边的左括号只能大于等于。
```
class Solution {
public:
    vector<string> ans;
    int stop;
        
    void dfs(int l, int r, string str) {
        if (r == stop) ans.push_back(str);
        else {
            if (l < stop) dfs(l + 1, r, str + "(");
            if (r < l) dfs(l, r + 1, str +")");
        }
    }

    vector<string> generateParenthesis(int n) {
        stop = n;
        dfs(0, 0, "");
        return ans;
    }
};
```


<h1 id="P4">P4</h1>
## 30. Substring with Concatenation of All Words
用unordered_map来代替trie数是很方便的。
枚举不同的初始起点，然后对于每个初始起点把串分成单词间隔，用双指针来求目标坐标。
如果是单字符我们知道用双指针，这里吧单个字符变成了单词，就用枚举起点转化为双指针。
两个实现都十分巧妙。
```
class Solution {
public:
    vector<int> findSubstring(string s, vector<string>& words) {
        vector<int> ans;
        if (s.size() == 0 || words.size() == 0) return ans;
        unordered_map<string, int> word;
        int k = words[0].size();
        for (auto &c: words) word[c]++;
        for (int t = 0; t < k; ++t) {
            int l = t, r = t + k, cnt = 0;
            unordered_map<string, int> word_used;
            while (r <= s.size()) {
                string now, lnow;
                for (int i = r - k; i < r; ++i) now += s[i];
                for (int i = l; i < l + k; ++i) lnow += s[i];
                if (word.count(now)) {
                    if (word_used[now] == word[now]) {
                        while (lnow != now) {
                            l += k;
                            word_used[lnow]--, cnt--;
                            lnow.clear();
                            for (int i = l; i < l + k; ++i) lnow += s[i];
                        }
                        l += k, r += k;
                    }
                    else word_used[now]++, cnt++, r += k;
                    if (cnt == words.size()) ans.push_back(l), l = l + k, cnt--, word_used[lnow]--;
                } else {
                    l = r, r = l + k;
                    cnt = 0;
                    word_used.clear();
                }
            }
        }
        return ans;
    }
};
```

***
## 31. Next Permutation
下一个排列，比当前排列大的最小排列，贪心变换，从右往左第一个下降，和右边最小的比它它的交换，然后它右边的取反。
```
class Solution {
public:
    void reverse(vector<int> &V, int i, int j) {
        while (i < j) {
            swap(V[i], V[j]);
            ++i, --j;
        }
    }
    
    void nextPermutation(vector<int>& nums) {
        int i = nums.size() - 1;
        for (; i > 0; --i) {
            if (nums[i] > nums[i - 1]) {
                int j;
                for (j = i + 1; j < nums.size() && nums[i - 1] < nums[j]; ++j) ;
                swap(nums[i - 1], nums[j - 1]);
                break;
            }
        }
        reverse(nums, i, nums.size() - 1);
    }
};
```


## 33 Search in Rotated Sorted Array
二分找横截面，二分找数字
```
class Solution {
public:
    int search(vector<int>& nums, int target) {
        if (nums.size() == 0) return -1;
        if (nums.size() == 1) return target == nums[0] ? 0 : -1;
        int l = 0, r = nums.size() - 1;
        while (l < r) {
            int mid = (l + r + 1) / 2;
            if (nums[mid] > nums[0]) l = mid;
            else r = mid - 1;
        }
        int t;
        vector<int>::iterator it = nums.begin();
        while (l--) ++it;
        if (nums[0] > target) t = lower_bound(++it, nums.end(), target) - nums.begin();
        else t = lower_bound(nums.begin(), ++it, target) - nums.begin();
        return nums[t] == target ? t : -1;
    }
};
```

## 34. Search for a Range
二分，这里边界靠左就左边界加，反之亦然。注意最左和最右边找不到要特判。
```
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> ans(2, -1);
        if (nums.size() == 0) return ans;
        int l = 0, r = nums.size() - 1;
        while (l < r) {
            int mid = (l + r + 1) / 2;
            if (nums[mid] < target) l = mid;
            else r = mid - 1;
        }
        if (nums[r] >= target) r--;
        if (r + 1 >= nums.size() || nums[r + 1] != target) return ans;
        ans[0] = r + 1;
        r = nums.size() - 1;
        while (l < r) {
            int mid = (l + r) / 2;
            if (nums[mid] > target) r = mid;
            else l = mid + 1;
        }
        if (nums[l] <= target) l++;
        ans[1] = l - 1;
        return ans;
    }
};
```
<h1 id="P5">P5</h1>

##42. Trapping Rain Water 
考虑没一列的最终水容量，取决于它左边最高的柱子和它右边最高的实体，这样用数组维护这两个值就可以了，复杂度$O(n)$.
```
class Solution {
public:
    int trap(vector<int>& height) {
        vector<int> left_max, right_max(height.size());
        int max_value = 0;
        for (int i = 0; i < height.size(); ++i) {
            left_max.push_back(max_value);
            max_value = max(max_value, height[i]);
        }
        max_value = 0;
        for (int i = height.size() - 1; i >= 0; --i) {
            right_max[i] = max_value;
            max_value = max(max_value, height[i]);
        }
        int water = 0;
        for (int i = 0; i < height.size(); ++i) {
            int side = min(left_max[i], right_max[i]);
            water += side > height[i] ? side - height[i] : 0;
        }
        return water;
    }
};
```

##43. Multiply Strings
大数乘法，还是错了一次，考虑其中一个为0。
大数都倒着处理比较容易。
通用函数reverse(iterator_begin, iterator_end)好用
```
class Solution {
public:
    void add(string &origin, string &now, int start) {
        int boost = 0, c;
        for (int i = 0; i < now.size(); ++i, ++start) {
            if (start >= origin.size()) origin += "0"; 
            c = now[i] - '0' + origin[start] - '0' + boost;
            origin[start] = c % 10 + '0';
            boost = c > 9 ? 1 : 0;
        }
        if (boost) origin += "1";
    }
    
    string single_multiply(char now, string &origin) {
        string ans;
        int boost = 0, a = now - '0', c;
        for (int i = 0; i < origin.size(); ++i) {
            int b = origin[i] - '0';
            c = b * a + boost;
            ans += c % 10 + '0';
            boost = c / 10;
        }
        if (boost) ans += boost + '0';
        return ans;
    }

    string multiply(string num1, string num2) {
        string ans;
        if (num1 == "0" || num2 == "0") return "0";
        reverse(num1.begin(), num1.end());
        reverse(num2.begin(), num2.end());
        for (int i = 0; i < num2.size(); ++i) {
            string down = single_multiply(num2[i], num1);
            add(ans, down, i);
        }
        reverse(ans.begin(), ans.end());
        return ans;
    }
};
```

##44. Wildcard Matching
dp[i][j]s串以i结尾，p串以j结尾是否匹配。
这里主要考虑*可以为空字符
```
class Solution {
public:
    bool isMatch(string s, string p) {
        vector<vector<bool> > dp(s.size() + 1, vector<bool>(p.size() + 1));
        dp[0][0] = true;
        if (p.size() != 0 && p[0] == '*') dp[0][1] = true;
        for (int i = 0; i <= s.size(); ++i) {
            for (int j = 1; j <= p.size(); ++j) {
                if (i > 0 && (s[i - 1] == p[j - 1] || p[j - 1] == '?')) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else if (p[j - 1] == '*') {
                    if (i > 0)
                        dp[i][j] = dp[i - 1][j] || dp[i - 1][j - 1];
                    dp[i][j] = dp[i][j] || dp[i][j - 1];
                }
            }
        }
        return dp[s.size()][p.size()];
    }
};
```

#45. Jump Game II
首先容易想到dp[i]，表示到i最少多少步，然后对每一个i，更新后面它可以到达的地方。
但是这样最坏复杂度平方。其实可以放发现，这里朝右边更新的指针，没有必要回移了，因为
回移之后的值只可能变大不会变小，那就没必要回移。这样复杂度O(n)

```
class Solution {
public:
    int jump(vector<int>& nums) {
        vector<int> dp(nums.size(), int(1e9));
        dp[0] = 0;
        int ans = int(1e9),j = 0;
        for (int i = 0; i < nums.size(); ++i) {
            for (; j - i <= nums[i] && j < nums.size(); ++j) {
                dp[j] = min(dp[j], dp[i] + 1);
            }
        }
        return dp[nums.size() - 1];
    }
};
```
## 46. Permutations
递归实现，每一个都和后面任意元素交换，然后递归。
```
class Solution {
public:
    void dfs(int k, vector<int> &nums, vector<vector<int> > &ans) {
        if (k == nums.size()) {
            ans.push_back(nums);
            return;
        }
        for (int i = k; i < nums.size(); ++i) {
            swap(nums[i], nums[k]);
            dfs(k + 1, nums, ans);
            swap(nums[i], nums[k]);
        }
    }

    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int> > ans;
        dfs(0, nums, ans);
        return ans;
    }
};
```

## 47. Permutations II
输出所有的不同的排列，交换的时候和没一种不同的元素只交换一次，第二次只会造成重复的结果。
```
class Solution {
public:
    void dfs(int k, vector<int> &nums, vector<vector<int> > &ans) {
        if (k == nums.size()) {
            ans.push_back(nums);
            return;
        }
        set<int> st;
        for (int i = k; i < nums.size(); ++i) {
            if (st.count(nums[i])) continue;
            swap(nums[i], nums[k]);
            dfs(k + 1, nums, ans);
            swap(nums[i], nums[k]);
            st.insert(nums[i]);
        }
    }

    vector<vector<int>> permuteUnique(vector<int>& nums) {
        vector<vector<int> > ans;
        dfs(0, nums, ans);
        return ans;
    }
};
```

<h1 id="P3">P3</h1>

***

##50 Pow(x, n)
快速幂，需要注意的是n为负数, int n最小的负数无法转换为正数。
```
class Solution {
public:
    double myPow(double x, int n) {
        double ans = 1;
        bool ok = false;
        long long k = n;
        if (n < 0) ok = true, k = -k;
        if (n == 0) return 1;
        while (k) {
            if (k & 1) ans *= x;
            k >>= 1;
            x = x * x;
        }
        if (ok) ans = 1 / ans;
        return ans;
    }
};
```
##54 Spiral Matrix
模拟螺旋路径，四个方向模拟就行
```
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector<int> ans;
        if (matrix.size() == 0) return ans;
        int x = 0, y = 0, n = matrix.size(), m = matrix[0].size();
        int dir = 0;
        int last = 0;
        while (true) {
            if (dir == 0) {
                while (y < m && matrix[x][y] != -123) {
                    ans.push_back(matrix[x][y]);
                    matrix[x][y] = -123;
                    y++;
                }
                x++, y--;
            } else if (dir == 1) {
                while (x < n && matrix[x][y] != -123) {
                    ans.push_back(matrix[x][y]);
                    matrix[x][y] = -123;
                    x++;
                }
                x--, y--;
            } else if (dir == 2) {
                while (y >= 0 && matrix[x][y] != -123) {
                    ans.push_back(matrix[x][y]);
                    matrix[x][y] = -123;
                    y--;
                }
                x--, y++;
            } else {
                while (x >= 0 && matrix[x][y] != -123) {
                    ans.push_back(matrix[x][y]);
                    matrix[x][y] = -123;
                    x--;
                }
                x++, y++;
            }
            dir = (++dir) % 4;
            if (ans.size() == last) break;
            last = ans.size();
        }
        return ans;
    }
};
```
***
## 56. Merge Intervals
区间合并，排序之后合并。
```
/**
 * Definition for an interval.
 * struct Interval {
 *     int start;
 *     int end;
 *     Interval() : start(0), end(0) {}
 *     Interval(int s, int e) : start(s), end(e) {}
 * };
 */
 
 bool cmp(Interval a, Interval b) {
     if (a.start == b.start) return a.end < b.end;
     return a.start < b.start;
 }
 
class Solution {

public:
    vector<Interval> merge(vector<Interval>& intervals) {
        sort(intervals.begin(), intervals.end(), cmp);
        vector<Interval> ans;
        int l = -1, r = -1;
        for (auto c: intervals) {
            if (l == -1) {
                l = c.start, r = c.end;
            } else {
                if (c.start > r) {
                    ans.push_back(Interval(l, r));
                    l = c.start, r = c.end;
                } else {
                    r = max(r, c.end);
                }
            }
        }
        if (l != -1) ans.push_back(Interval(l, r));
        return ans;
    }
};
```

***
##62. Unique Paths(dp)
**题意**:从格子的左上角走到右下角有多少种走法,只能往右或者往下走.
**分析**:直接dp即可.这里主要注意当dp写成递推的时候,更新顺序的选择时候道路方向相关的.
```
class Solution {
public:
    int uniquePaths(int m, int n) {
        int dp[111][111];
        memset(dp, 0, sizeof(dp));
        dp[m][n] = 1;
        
        for (int i = m ; i > 0; i--) {
            for (int j = n; j > 0; j--) {
                if (j + 1 <= n) dp[i][j] += dp[i][j + 1];
                if (i + 1 <= m) dp[i][j] += dp[i + 1][j];
            }
        }
        return dp[1][1];
    }
};
```
***
##63. Unique Paths II(dp)
类似上题而已...
```
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        int dp[111][111], m = obstacleGrid.size(), n = obstacleGrid[0].size();
        memset(dp, 0, sizeof(dp));
        dp[m][n] = 1;
        
        for (int i = m ; i > 0; i--) {
            for (int j = n; j > 0; j--) {
                if (j + 1 <= n && !obstacleGrid[i - 1][j - 1] ) dp[i][j] += dp[i][j + 1];
                if (i + 1 <= m && !obstacleGrid[i - 1][j - 1]) dp[i][j] += dp[i + 1][j];
            }
        }
        return obstacleGrid[m - 1][n - 1] == 1 ? 0 : dp[1][1];
    }
};
```

***
##64. Minimum Path Sum（经典动态规划）
**题意**：求矩阵左上角到右下角的路径的数字和最小。
**分析**:
定义：$dp[i][j]$表示从$(i,j)$点到右下角的最短距离。
除了最后一行没后下面的数，最后一列没有右边的数特殊初始化，其它的全部都是:
$$dp[i][j] = grid[i][j] + min(dp[i + 1][j], dp[i][j + 1])$$
这里发现grid只用了一次，直接连dp数组都不要，用grid来dp就好了。
```
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        if (grid.empty()) return 0;
        int n = grid.size(), m = grid[0].size();
        for (int i = m - 2; i >= 0; i--) grid[n - 1][i] = grid[n - 1][i] + grid[n - 1][i + 1];
        for (int i = n - 2; i >= 0; i--) grid[i][m - 1] = grid[i][m - 1] + grid[i + 1][m - 1];
        for (int i = n - 2; i >= 0; i--) {
            for (int j = m - 2; j >= 0; j--) {
                grid[i][j] = grid[i][j] + min(grid[i + 1][j], grid[i][j + 1]);
            }
        }
        return grid[0][0];
    }
};
```

***
##70.  Climbing Stairs(水dp)
非常水的动态规划,定义$dp[i]$为爬到$i$梯需要的步数,归纳为它的两个子问题即可.
```
class Solution {
public:
    int climbStairs(int n) {
        int dp[1000000];
        dp[0] = dp[1] = 1;
        for (int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[n];
    }
};
```
***
##98. Validate Binary Search Tree(dfs)
**题意**:判断一个二叉树是不是二叉搜索树.
**分析**:中序遍历二叉搜索树,将得到升序数组,dfs遍历即可.

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
private:
    bool ans;
    long long last;
public:
    void dfs(TreeNode *cur) {
        if (!ans) return;
        if (cur -> left != NULL) dfs(cur -> left);
        if (cur -> val <= last) ans = false;
        last = cur -> val;
        if (cur -> right != NULL) dfs(cur -> right);
        return;
    }

    bool isValidBST(TreeNode* root) {
        ans = true, last = -10000000000000;
        if (root == NULL) return ans;
        dfs(root);
        return ans;
    }
};


```
##118. Pascal's Triangle (模拟)
生成帕斯卡三角形，很水。

```
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int> > ret;
        vector<int> temp;
        if (numRows > 0) temp.push_back(1), ret.push_back(temp);
        for (int i = 1; i < numRows; i++) {
            vector<int> temps;
            for (int j = 0; j < ret[i - 1].size() + 1; j++) {
                if (j == 0 || j == ret[i - 1].size()) temps.push_back(1);
                else temps.push_back(ret[i - 1][j - 1] + ret[i - 1][j]);
            }
            ret.push_back(temps);
        }
        return ret;
    }
};
```

##120. Triangle(dp)
**题意**:经典DP问题,有点像个二叉树的树形dp.
```
class Solution {
public:
    int minimumTotal(vector<vector<int>>& v) {
        int dp[222][222];
        
        for (int i = v[v.size() - 1].size() - 1; i >= 0; i--) {
            dp[v.size() - 1][i] = v[v.size() - 1][i];
        }
        for (int i = v.size() - 2; i >= 0; i--) {
            for (int j = v[i].size() - 1; j >= 0; j--) {
                dp[i][j] = v[i][j] + min(dp[i + 1][j], dp[i + 1][j + 1]);
            }
        }
        return dp[0][0];
    }
};
```

***
##179. Largest Number(排序 + 贪心)
**题意**:给你多个数字要求拼接成最大的数字。
**分析**：
简单的想法就是按字典序由大到小排序，然后拼接起来，但是这样不对，比如：30和3，330比303更优秀。这里有个巧妙的比较方法就是对于谁在前谁在后拼接起来试一下就可以。整个比较链是传递的，所以这种方法排序就可。
注意其中的特殊情况就是多个0，需要排除，这里自己没有排除Wa了一发，多考虑漏洞啊。

```
class Solution {
public:
    static bool cmp(string &a, string &b) {
        return a + b > b + a;
    }

    string largestNumber(vector<int>& nums) {
        vector<string> temp;
        for (auto &c: nums) temp.push_back(to_string(c));
        sort(temp.begin(), temp.end(), cmp);
        string ret;
        for (auto &c: temp) ret += c;
        if (!ret.empty() && ret[0] == '0') return "0";
        return ret;
    }
};
```
*** 
## 205. Isomorphic Strings   Add to List QuestionEditorial Solution  My Submissions
一个源点只能有一个像的双射。
```
class Solution(object):
    def isIsomorphic(self, s, t):
        """
        :type s: str
        :type t: str
        :rtype: bool
        """
        if len(s) != len(t):
            return False
        a = [-1] * 256
        b = [-1] * 256
        for i in range(len(s)):
            x = ord(s[i])
            y = ord(t[i])
            if a[x] == -1 and b[y] == -1:
                a[x] = y
                b[y] = x
            elif a[x] != y:
                return False
        return True
```


***
##208. Implement Trie (Prefix Tree)(trie树)
**题意**:支持插入查询,和前缀查询的trie树实现.
**分析**:设置一个前缀标记就好了.
这里应该记住一个经典操作,trie树中的迭代.
> ```
>  if ((p -> next)[word[i] - 'a'] == NULL) (p -> next)[word[i] - 'a'] = new TrieNode;
> p = (p -> next)[word[i] - 'a'];
> ```

**code**:
```

class TrieNode {
public:
    // Initialize your data structure here.
    TrieNode() : isword(false), ispreix(false) {
        for (int i = 0; i < 26; i++) next[i] = NULL;
    }
    bool isword, ispreix;
    TrieNode *next[26];
};

class Trie {
public:
    Trie() {
        root = new TrieNode();
    }

    // Inserts a word into the trie.
    void insert(string word) {
        TrieNode *p = root;
        for (int i = 0; i < word.size(); i++) {
            if ((p -> next)[word[i] - 'a'] == NULL) (p -> next)[word[i] - 'a'] = new TrieNode;
            p = (p -> next)[word[i] - 'a'];
            (p -> ispreix) = true;
        }
        (p -> isword) = true;
    }

    // Returns if the word is in the trie.
    bool search(string word) {
        TrieNode *p = root;
        for (int i = 0; i < word.size(); i++) {
            if ((p -> next)[word[i] - 'a'] == NULL) return false;
            p = (p -> next)[word[i] - 'a'];
            if (i == word.size() - 1) return (p -> isword);
        }
    }

    // Returns if there is any word in the trie
    // that starts with the given prefix.
    bool startsWith(string word) {
        TrieNode *p = root;
        for (int i = 0; i < word.size(); i++) {
            if ((p -> next)[word[i] - 'a'] == NULL) return false;
            p = (p -> next)[word[i] - 'a'];
            if (i == word.size() - 1) return (p -> ispreix);
        }
    }

private:
    TrieNode* root;
};


```
***
##211.Add and Search Word - Data structure design(trie树)
**题意**:创建一个单词库,支持两种操作,给库里面加入新单词和查询某单词是否在库中,但是注意用'.'来做通配符,可以代替任意字符.
**分析**:trie树的改造.主要是通配符的处理.这里把搜索函数处理了一下,用递归实现,当配件通配符号的时候遍历它的所有26个分枝来求满足条件的.

**code**:
```cpp

class WordDictionary {
private:
    struct node {
        int have;
        node *next[26];
        
        node(void) {
            have = 0;
            for (int i = 0; i < 26; i++) next[i] = NULL;
        }
    };
    
public:
    
    node *root = new node;

    // Adds a word into the data structure.
    // The time consumer is O(n)
    void addWord(string word) {
        node *p = root;
        for (int i = 0; i < word.size(); i++) {
            int aim = word[i] - 'a';
            if ((p -> next)[aim] == NULL) (p -> next)[aim] = new node;
            p = (p -> next)[aim];
        }
        (p -> have) = (p -> have) + 1;
    }
    
    bool can(node *p, int head, const string &word) {
        if (head == word.size() && p -> have) return true;
        int aim = word[head] == '.' ? 26 : word[head] - 'a';
        if (aim == 26) {
            for (int i = 0; i < 26; i++)
                if ((p -> next)[i] != NULL && can( (p -> next)[i], head + 1, word)) return true;
            return false;
        }
        if ((p -> next)[aim] != NULL)
            return can((p -> next)[aim], head + 1, word);
        return false;
    }
    
    // Returns if the word is in the data structure. A word could
    // contain the dot character '.' to represent any one letter.
    bool search(string word) {
        node *p = root;
        int head = 0;
        return can(p, head, word);
    }
};

// Your WordDictionary object will be instantiated and called as such:
// WordDictionary wordDictionary;
// wordDictionary.addWord("word");
// wordDictionary.search("pattern");



```

***
##242. Valid Anagram (字符串本质)
排序比较即可。
有求字符串最小表示的算法，复杂度是$O(n)$，这里不表。
```
class Solution {
public:
    bool isAnagram(string s, string t) {
        if (s.size() != t.size()) return false;
        sort(s.begin(), s.end());
        sort(t.begin(), t.end());
        for (int i = 0; i < s.size(); i++) {
            if (s[i] != t[i]) return false;
        }
        return true;
    }
};
```

***
##264. Ugly Number II(dp,O(n)求丑数)
**题意**:求第n个丑数.
**分析**:很容易有set的做法,复杂度$nlogn$.但是这里可以用$O(n)$的做法.
- 我们定义$dp[i]$为第$i$个丑数,那么$dp[i]$一定是前面的某个丑数乘以2或者乘以3或者乘以5得到的最小值.
- 我们定义指针:p2,$dp[p2]$表示还没有乘以2来派生下一个数的最小丑数,同理有$dp[p3],dp[p5]$.这样我们就维护了伤着派生情况的最小值,每次都只需要从三个派生口找一个最小的来更新.
- 派送口的更新规则是一旦该口派生了,指针就指向下一个丑数,成为新的没有派生的最小丑数.

**总结**:这个思路还是极其巧妙的,有三条派生流,每次更新流的端口.
```
typedef long long ll;

class Solution {
public:

    ll nthUglyNumber(int n) {
        //priority_queue<int, vector<int>, greater<int> > que;
        set<ll> que;
        que.insert(1);
        for (int i = 1; i < n; i++) {
            ll top = *que.begin(); que.erase(top);
            if (!que.count(top * 2)) que.insert(top * 2);
            if (!que.count(top * 3)) que.insert(top * 3);
            if (!que.count(top * 5)) que.insert(top * 5);
        }
        return *que.begin();
    }
};
```

```
typedef long long ll;

class Solution {
public:

    ll nthUglyNumber(int n) {
        //priority_queue<int, vector<int>, greater<int> > que;
        ll ans[n];
        ans[0] = 1;
        int p2 = 0, p3 = 0, p5 = 0;
        for (int i = 1; i < n; i++) {
             ans[i] = min(ans[p2] * 2, min(ans[p3] * 3, ans[p5] * 5));
             if (ans[i] / ans[p2] == 2) p2++;
             if (ans[i] / ans[p3] == 3) p3++;
             if (ans[i] / ans[p5] == 5) p5++;
        }
        return ans[n - 1];
    }
};
```

***
**题意**:给你一个数n,问你求组合为它的平方数的最小的个数
**分析**:有小到大的枚举平方数,然后用完全背包的思想即可,复杂度$O(n)\sqrt{n}$
```
class Solution {
public:
    int numSquares(int n) {
        int dp[1000000];
        for (int i = 0; i <= n; i++) dp[i] = int(1e10);
        dp[0] = 0;
        for (long long i = 1; i * i <= n; ++i) {
            for (int j = i * i; j <= n; j++) {
                dp[j] = min(dp[j], dp[j - i *i] + 1);
            }
        }
        return dp[n];
    }

};

```

***
##303. Range Sum Query - Immutable(水dp)
无它,水区间和...
```
class NumArray {
public:
    long long dp[100000];
    int size;
    NumArray(vector<int> &nums) {
        if (!nums.size()) return;
        dp[0] = nums[0];
        size = nums.size();
        for (int i = 1; i < nums.size(); i++) {
            dp[i] = 0;
            dp[i] = dp[i - 1] + nums[i];
        }
    }

    long long sumRange(int i, int j) {
        if (!size) return 0;
        if (!i) return dp[j];
        else return dp[j] - dp[i - 1];
    }
};


// Your NumArray object will be instantiated and called as such:
// NumArray numArray(nums);
// numArray.sumRange(0, 1);
// numArray.sumRange(1, 2);
```

