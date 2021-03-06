[TOC]

# 学习笔记

## 每日一题
### 每日一题-198-打家劫舍
**分析递推关系**  
分析小偷站在第N间房子面前面临的选项:   
1. 不偷, 意味着可以获取N-1间房子获得的最大收益
2. 偷, 意味着可以获取N-2间房子获得的最大收益 + N间房子的收益
3. 最后就看 是偷合适 还是不偷合适, 比大小

**分析重复项**  
分析方法一, 可以发现重复计算项, 假设nums = [1,2,3,1,4,3,2] , length = 6 
```C
rob(6) = Math.max(rob(5),rob(4)+nums[6])
rob(5) = Math.max(rob(4),rob(3)+nums[5])
rob(4) = Math.max(rob(3),rob(2)+nums[4])
										  rob(6)
								    /	    	\
               rob(5)       rob(4)
              /     \       /    \
           rob(4) rob(3)  rob(3) rob(2)
```
使用memory优化, 数组保存, index标识第几栋房子, value标识index房子的最多可以偷到的数  

**分析动态规划, 从下往上**
1. 明确`记忆中间重复项` 的含义, 在这个题目中, 数组index标识第几栋房子, value表示在index房子处能偷到的最大值  
2. 明确 base case , dp[0] = 0, dp[1] = nums[1]  

## 方法三:从下往上: 递推 + 记忆中间重复项
1. 明确`记忆中间重复项` 的含义, 在这个题目中, 数组index标识第几栋房子, value表示在index房子处能偷到的最大值  
2. 明确 base case

二维数组memory
```java
    public int rob(int[] nums) {
        if (nums.length < 1) { return 0; }
        int[][] dp = new int[nums.length+1][2];// 0 不偷 1偷
        //base case
        dp[0][0] = 0;
        dp[1][1] = nums[0];
        int index = 2;
        for (int i = 1; i < nums.length; ++i) {
            //不偷当前, 可以偷前一个
            dp[index][0] = Math.max(dp[index-1][0],dp[index-1][1]);
            //偷当前, 前一个不可以
            dp[index][1] =  dp[index-1][0] + nums[i];
            ++index;
        }
        return Math.max(dp[index-1][0],dp[index-1][1]);
    }
```

**memory简化为一维数组, 为什么可以简化呢**  
示例: [1,2,3,1]   
1. 最终的结果是通过 `return Math.max(dp[index-1][0],dp[index-1][1]);`
2. `dp[index-1][0]` <- `dp[index][0] = Math.max(dp[index-1][0],dp[index-1][1]);`
3. `dp[index-1][1]` <- `dp[index-1][0] + nums[i]`
4. 就是这三个比出最大的 `dp[index-1][0]`  `dp[index-1][1]`  `dp[index-1][0] + nums[i]`
5. `dp[index-1][0] + nums[i]` 肯定比  `dp[index-1][0]` 大, 数组中数值都是非负整数
6. 就只需要比  `dp[index-1][1]`  `dp[index-1][0] + nums[i]`
7. 由此得到 `dp[index] = Math.max(dp[index-1],dp[index-2] + nums[cur]);`

```java
    public int rob(int[] nums) {
        //1. 假如当前处于N
        //2. 就面临在N这边偷还是不偷
        //3. 就是比较dp[N-1] 和 dp[N-2] + nums[N]
        //4. 如果dp[N-1]比较大, 就说明N不用偷
        //5. 如果 dp[N-2] + nums[N]比较大, 就说明偷第N比较大
        if (nums.length < 1) { return 0; }
        int[] dp = new int[nums.length+1];
        //哨兵  dp[0] = 0
        dp[0] = 0;
        dp[1] = nums[0];
        int index = 2;
        //遍历数组
        for (int cur = 1;cur < nums.length; ++cur) {
            dp[index] = Math.max(dp[index-1],dp[index-2] + nums[cur]);
            ++index;
        }
        return dp[nums.length];
    }
```
### 复杂度
* 时间复杂度: O(N)
* 空间复杂度: O(N)

## 方法二: 从下往上: 递推 + 记忆中间重复项(使用N个指针记录)
优化 DP 方法
```java
    public int rob(int[] nums) {
        //类似于斐波拉契的改进
        if (nums.length < 1) { return 0; }
        // dp0 dp1 num
        int dp0 = 0;
        int dp1 = nums[0];
        for (int i = 1; i < nums.length; i++) {
            int dpNext = Math.max(dp1,dp0+nums[i]);
            dp0 = dp1;
            dp1 = dpNext;
        }
        return dp1;
    }
```
### 复杂度
* 时间复杂度: O(N)
* 空间复杂度: O(1)
[每日一题-198-打家劫舍.md](./每日一题-198-打家劫舍.md)

### 每日一题-53-最大子序和
**分治**  
```C
分治不仅可以解决区间 [0,n−1]，还可以用于解决任意的子区间 [l,r] 的问题。如果我们把 [0,n−1] 分治下去出现的所有子区间的信息都用堆式存储的方式记忆化下来，即建成一颗真正的树之后，我们就可以在 O(logn) 的时间内求到任意区间内的答案，我们甚至可以修改序列中的值，做一些简单的维护，之后仍然可以在 O(logn) 的时间内求到任意区间内的答案，对于大规模查询的情况下，这种方法的优势便体现了出来。这棵树就是上文提及的一种神奇的数据结构——线段树
```
**好处:**
1. 还可以用于解决任意的子区间 [l,r] 的问题
2. 分治到达 start >= end 之后, 则在函数调用栈中已经形成了一棵完整的二叉树
3. 我们就可以在 O(logn) 的时间内求到任意区间内的答案
4. 我们甚至可以修改序列中的值，做一些简单的维护
[每日一题-53-最大子序和.md](./每日一题-53-最大子序和.md)  


[每日一题-322-零钱兑换.md](./每日一题-322-零钱兑换.md)  
[每日一题-312-戳气球.md](./每日一题-312-戳气球.md)  
[每日一题-221-最大正方形.md](./每日一题-221-最大正方形.md)  
[每日一题-120-三角形最小路径和.md](./每日一题-120-三角形最小路径和.md)  
[每日一题-64-最小路径和.md](./每日一题-64-最小路径和.md)  
[每日一题-621-任务调度器.md](./每日一题-621-任务调度器.md)  
[每日一题-434-字符串中的单词数.md](./每日一题-434-字符串中的单词数.md)  
[每日一题-3-无重复字符的最长子串.md](./每日一题-3-无重复字符的最长子串.md)  

## 动态规划题目的思考历程
1. 找到递归关系
2. 从上往下: 写出暴力递归解
3. 从上往下: 写出暴力递归解 + 记忆中间重复项
4. 从下往上: 递推 + 记忆中间重复项
5. 从下往上: 递推 + 记忆中间重复项(使用N个指针记录)

入门的时候先写暴力递归解, 有助于了解递归树, 知道重复子问题出现在什么地方, 便于通过记录优化  

## 动态规划题目解题步骤
1. 将大问题拆分成子问题
2. 明确状态树
3. 明确 memory 的含义, 状态设计（特别重要）
4. 推导状态转移方程（特别重要）
5. 明确base case
6. 考虑输出
[动态规划(理解无后效性)](https://leetcode-cn.com/problems/maximum-product-subarray/solution/dong-tai-gui-hua-li-jie-wu-hou-xiao-xing-by-liweiw/)

## 感悟
1. 动态规划一般需要找寻子问题, 子问题的解很容易就能算出来,形成为base case
2. 子问题发展为原问题的过程中, 虽然可能性很多, 但是你拥有上帝视角, 可以知道所有的发展的可能性
3. 子问题发展为原问题的过程中, 会存在重复的路径, 所以可以使用memory记录, 解决重复计算的问题
4. 可以使用memory帮助选择满足需求的路径

### 形象对比
**贪心：**一条路走到黑，就一次机会，只能哪边看着顺眼走哪边
**回溯：**一条路走到黑，无数次重来的机会，还怕我走不出来 (Snapshot View)
**动态规划：**拥有上帝视角，手握无数平行宇宙的历史存档， 同时发展出无数个未来 (Versioned Archive View)

## 动态规划优势
递归的解法 (回溯) 是不可以放在生产环境的，因为我们很难控制问题规模的大小，无法预料何时会有爆栈的风险, 所以动态规划通过迭代推理更方安全些.   
[01-动态规划.md](./01-动态规划.md)

## 超哥视频中的题目
### 爬楼梯 
1. 每次可以爬 1 步 ,  2 步,  3 步, 请问第n步有多少种走法
```java
    //每次可以行走 1 2 3
    public int climbStairs2(int n) {
        if (n < 3) { return n; }
        int dp1 = 1;
        int dp2 = 2;
        int dp3 = 4;
        int index = 4;
        while (index <= n) {
            int dpNext = dp1 + dp2 + dp3;
            dp1 = dp2;
            dp2 = dp3;
            dp3 = dpNext;
        }
        return dp3;
    }
```
### 时间复杂度
* 时间复杂度: O(N)
* 空间复杂度: O(1)

2. 每次可以爬 1 步 ,  2 步,  3 步, 并且相邻的步数不可以相同, 请问第n步有多少种走法
```java
    //每次可以行走 1 2 3, 但是相邻的步数不能相同
    public int climbStairs3(int n) {
        if (n < 3) { return 1; }
        //row标识第几个台阶 col标识 走几步
        //dp宽度为4是因为每次可以走 1 2 3, 多出来的dp[n][0]存储对应的n有多少种走法
        //牺牲了第0行, 没有存放任何数据, 为了代码的阅读性, 4为int值的大小
        int[][] dp = new int[n+1][4];
        //base case
        dp[1][0] = 1; dp[1][1] = 1;
        dp[2][0] = 1; dp[2][2] = 1;
        dp[3][0] = 3; dp[3][1] = 1; dp[3][2] = 1; dp[3][3] = 1;
        for (int stage = 4; stage <= n; stage++) {
            int curStageWays = 0;
            for (int i = 1; i <= 3; i++) {
                int preStage = stage - i;
                int waysForCurSteps = 0;
                //j 标识待走的数
                for (int j = 1; j <= 3; j++) {
                    if (i == j ) {
                        continue;
                    }
                    waysForCurSteps += dp[preStage][j];
                    curStageWays += dp[preStage][j];
                }
                dp[stage][i] = waysForCurSteps;
            }
            dp[stage][0] = curStageWays;
        }
        return dp[n][0];
    }
```
### 时间复杂度
* 时间复杂度: O(N*9)-> O(N)
* 空间复杂度: O(N*4)-> O(N)