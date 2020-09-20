[toc]

# Leetcode刷题

## 动态规划

## 贪心

## 搜索

## 回溯

## 双指针

## 滑动窗口

## 前缀和

## 单调队列

## 字符串

### `Leetcode 647. ` 回文子串

**问题描述**

给定一个字符串，你的任务是计算这个字符串中有多少个回文子串。

具有不同开始位置或结束位置的子串，即使是由相同的字符组成，也会被视作不同的子串。

 

示例 1：

输入："abc"
输出：3
解释：三个回文子串: "a", "b", "c"

**解题报告**

**方法一：动态规划**

`dp[i][j]` 表示子串 `s[i:j]` 是否是回文串
$$
dp[i][j]=\left\{\begin{matrix}
dp[i+1][j-1] \;\;\;\;if(s[i]==s[j])
\\ 
false\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;if(s[i]!=s[j])
\end{matrix}\right.
$$

时间复杂度：$O(n^2)$

空间复杂度: $O(n^2)$

当然了，还有另外一种时间复杂度在 $O(n^2)$，空间复杂度在 $O(1)$ 的方法。

**方法二：Manacher** 

首先进行字符串预处理，在原始字符串的空隙处插入符号 `#`

`dp[i]` 表示以 `i` 为中心的最长回文串的长度。

时间复杂度：$O(n)$

空间复杂度：$O(n)$

**实现代码**

**方法一：动态规划**

```cpp
class Solution {
public:
    int countSubstrings(string s) {
        int n=s.size();
        vector<vector<int>>dp(n, vector<int>(n, 0));
        int ans=0;
        for(int len=0;len<n;len++){
            for(int i=0;i+len<n;i++){
                int j=i+len;
                if(s[i]==s[j]&&(j-i<=2||dp[i+1][j-1])){
                    dp[i][j]=true;
                    ans++;
                }
            }
        }
        return ans;
    }
};
```

```cpp
class Solution{
    public:
        int countSubstrings(string s){
            int n=s.size();
            int ans=0;
            for(int i=0;i<n;i++){
                int l=i, r=i;
                while(1){
                    ans++;
                    l--;
                    r++;
                    if(l<0||r>n)break;
                    if(s[l]!=s[r])break;
                }
            }
            for(int i=0;i<n-1;i++){
                int l=i, r=i+1;
                if(s[l]!=s[r])continue;
                while(1){
                    ans++;
                    l--;
                    r++;
                    if(l<0||r>n)break;
                    if(s[l]!=s[r])break;
                }
            }
            return ans;
        }
};
```

**方法二：Manacher**

```cpp
class Solution{
    public:
        int countSubstrings(string s){
            string ns="@#";
            for(auto str:s){ns+=str;ns+='#';}
            ns+='!';
            s=ns;
            int sz=s.size()-1, ans=0;
            vector<int>dp(sz,1);
            int idx=0,mx=0;
            for(int i=1;i<sz;i++){
                if(i<mx)dp[i]=min(dp[2*idx-i], mx-i+1);
                else dp[i]=1;
                // 当i取0时，dp[i]取1，此时 i-dp[i]=-1
                while(s[i-dp[i]]==s[i+dp[i]]) dp[i]++;
                if(mx<i+dp[i]-1){
                    mx=i+dp[i]-1;
                    idx=i;
                }
                //cout<<dp[i]<<" ";
                ans+=(dp[i]/2);
            }
            return ans;
        }
};
```

**参考资料**

[Leetcode 647 回文子串](https://leetcode-cn.com/problems/palindromic-substrings)

### `Leetcode 730.` 统计不同回文子序列

$$
dp[i][j]=\left\{\begin{matrix}
dp[i+1][j]+dp[i][j-1]-dp[i+1][j-1] \;if(s[i]!=s[j])
\\ 
dp[i+1][j-1]*2+\left\{\begin{matrix}
2\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;if(没有找到与s[i] 重复的字符)
\\ 
1\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;if(只有一个与s[i] 重复的字符)
\\ 
-dp[l+1][r-1] \;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;\;if(存在多个与 s[i] 重复的字符)
\end{matrix}\right.
\end{matrix}\right.
$$



```cpp
int countPalindromicSubsequences(char * S){
    if(S == NULL || strlen(S) == 0)
        return 0;
    long long mod = 1000000007;
    int len = strlen(S);
    long dp[len][len];
    memset(dp, 0x0, sizeof(dp));

    for(int i = len - 1; i >= 0; i--)
    {
        dp[i][i] = 1;
        for(int j = i + 1; j < len; j++)
        {
            if(S[i] != S[j])
                dp[i][j] = dp[i + 1][j]/*以i+1为起点，j为末尾的字符串回文数*/ + dp[i][j - 1]/*以i为起点，j - 1为末尾的字符串回文数*/ - dp[i + 1][j - 1]/*以i+1为起点，j-1为末尾的字符串回文数被统计了两次*/;
            else
            {
                dp[i][j] = dp[i + 1][j - 1] * 2;
                int l = i + 1, r = j - 1;
                while(l <= r && S[l] != S[i]) l++; /* 从左侧开始查找相同的元素 */
                while(l <= r && S[r] != S[j]) r--; /* 从右侧开始查找相同的元素 */
                if(l > r) /* 没有找到重复字符 */
                    dp[i][j] += 2;
                else if(l == r) /* 只有一个重复字符 */
                    dp[i][j] += 1;
                else /* 找到重复字符了 */
                    dp[i][j] -= dp[l + 1][r - 1];
            }

            dp[i][j] = (dp[i][j] + mod) % mod;
            /*
                这是因为dp[i][j]是一个取模后的值，会有可能减去取模前的值变成一个负数，所以直接用
                dp[i][j] %= mod 是不对的，这样会依然为负数，需要加上mod偏移量，保证在结果在0-mod之间。
            */
        }
    }

    return dp[0][len - 1];
}

//作者：375D
//链接：https://leetcode-cn.com/problems/count-different-palindromic-//subsequences/solution/qu-jian-dp-by-375c-2/
//来源：力扣（LeetCode）
//著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

### `Leetcode 516.`  最长回文子序列

 [Leetcode 516.最长回文子序列](https://leetcode-cn.com/problems/longest-palindromic-subsequence/)

### `Leetcode 5. ` 最长回文子串

### `Leetcode 132.` 分割回文串 Ⅱ

## 其他

### `Leetcode 60.` 第 `k` 个排列

**问题描述**

给出集合 `[1,2,3,…,n]`，其所有元素共有 `n!` 种排列。

按大小顺序列出所有排列情况，并一一标记，当 n = 3 时, 所有排列如下：

"123"
"132"
"213"
"231"
"312"
"321"
给定 n 和 k，返回第 k 个排列。

说明：

给定 n 的范围是 [1, 9]。
给定 k 的范围是[1,  n!]。
示例 1:

输入: n = 3, k = 3
输出: "213"

**解题报告**

`a[i]` 表示第 `i` 位【从右到左数】取 `1` 的排列数。

#### 实现代码

```cpp
class Solution {
public:
    string getPermutation(int n, int k) {
        vector<int>a={1, 1, 2, 6, 24, 120, 720, 5040, 40320, 362880};
        vector<bool>vis(n+1, false);
        string ans;
        for(int i=n-1;i>=0;i--){
            int cnt=a[i];
            for(int j=1;j<=n;j++){
                if(vis[j])continue;
                if(k>cnt){k-=cnt;continue;}
                vis[j]=true;
                ans+=to_string(j);
                break;
            }
        }
        return ans;
    }
};
```

**参考文献**

[Leetcode 60. 第 `k` 个排列](https://leetcode-cn.com/problems/permutation-sequence)

[题解区 liweiwei1419的评论区](https://leetcode-cn.com/problems/permutation-sequence/solution/hui-su-jian-zhi-python-dai-ma-java-dai-ma-by-liwei/)



# 前端相关

## 前端框架的渲染过程

1. 浏览器解析 `HTML` 生成 `DOM` 树。

   大致流程就是浏览器使用词法分析器和解析器将 `HTML` 内容解析成语法树，也就是 `DOM` 树

2. 浏览器将 `CSS` 资源解析成 `CSS Rule Tree`。

   类似于 `HTML` 解析。

3. `JS` 通过 `DOM API` 和 `CSSOM API` 来操作 `DOM` 树和 `CSS` 树

4. 解析完后综合 `DOM` 树和 `CSS` 树

## vue react angular三大框架对比

`vue.js` 框架是目前较火的 `MVVM` 框架之一，简单易上手的学习曲线，只需要掌握简单的 `html`、`css`、`js` 即可上手；友好的官方文档，且 `vue` 社区活跃，使用者较多；配套的构建工具，`vue-cli` 这个脚手架工具将所有的开发环境配置好了，开发者直接进行开发。

### vue 与 react

相同点：

1. 都使用 `Virtual DOM`。通过虚拟 `DOM` 结合 `diff` 算法，我们可以很好地解决 `DOM` 操作的性能问题，即 **生成虚拟 `DOM` 的时间 + `diff` 算法时间 + `patch` 时间 < 修改 `DOM` 的时间**。
2. 都使用了 响应式和组件化 的视图组件。组件大大提高了代码的复用性。
3. 两者均专注于视图框架，将其他功能如 **路由和全局状态管理** 交给相关的库，如 `router` 和 `vuex` 等
4. 两者都不需要触碰 `DOM`。

不同点：

1. `vue` 比 `react` 更容易上手。学习 `vue`，我们只需要掌握中级的 `html` 、`css`、`js` 即可。但是 `react` 的学习会更加复杂一些，`react` 需要有 `ES6` 的基础，另外对于 `JSX` 语法，还有一定的学习成本。
2. `vue` 的优化做的要比 `react` 好一些。在 `react` 应用中，当某个组件的状态发生变化时，它会以根组件为根，重新渲染整个组件子树。**如要避免不必要的子组件的重渲染**，你需要在所有可能的地方使用 `PureCompnent` 或者手动实现 `shouldComponentUpdate` 方法。同时你可能会使用不可变的数据结构来使得你的组件更容易被优化；但是在做 `vue` 相关的项目，我们只需要关注业务逻辑，而不需要去操心是否会产生不必要的组件渲染。

### vue 与 angular

相同点：

1. `angular` 和 `vue` 都使用了指令。指令是一个比较方便的操作。
2. `angular` 和 `vue` 都使用了双向数据绑定。

不同点：

1. `vue` 更容易上手。`angular` 需要学习 `typescript`，上手难度较大；
2. 性能上来说，`vue` 更好些。`angular` 依赖于定时的脏检查来进行视图的更新，当 `watcher` 越来越多时，脏检查越来越慢，作用域中的每一次变化，所有的 `watcher` 都要重新计算，并且如果一些 `watcher` 出发另一个更新，脏检查循环可能要运行多次。而 `VUE` 中则没有这个问题，因为它使用基于依赖追终的观察系统并且异步队列更新，所有的数据变化都是独立差法，除非它们之间有明确的依赖关系。

## vue渲染



# Docker

Docker是一种轻量级的**虚拟化技术**

![img](https://img2018.cnblogs.com/blog/720430/201812/720430-20181226134430830-648875754.png)
