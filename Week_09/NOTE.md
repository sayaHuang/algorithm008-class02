[TOC]
# 学习笔记
## 每日一题
### 不同路径 II 

1. dp的含义: `int[][] dp = new int[row+1][col+1];`
2. row表示Grid的row+1, 同理得col, 多增加得一行作为base case
3. base case: `dp[1][1] = 1;`
4. 状态转移方程: 
  * 没有障碍物`dp[r+1][c+1] = dp[r][c+1] + dp[r+1][c];`
  * 有障碍物 `dp[r+1][c+1] = 0;`
```java
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if (obstacleGrid.length < 1 || obstacleGrid[0].length < 1) { return 0;}
        int row = obstacleGrid.length;
        int col = obstacleGrid[0].length;
        int[][] dp = new int[row+1][col+1];
        if (obstacleGrid[0][0] == 1) { return 0; }
        dp[1][1] = 1;
        for (int r = 0; r < row; ++r) {
            for (int c = 0; c < col; ++c) {
                if (r == 0 && c == 0) {continue;}
                if (obstacleGrid[r][c] == 0) {
                    dp[r+1][c+1] = dp[r][c+1] + dp[r+1][c];
                } else {
                    dp[r+1][c+1] = 0;
                }
            }
        }
        return dp[row][col];
    }
```
### 买卖股票的最佳时机 IV
```java
  //int[i+1][k][0/1] dp i: 表示第几天, k: 表示还剩得交易次数, 0 / 1: 0表示没有持有股票 1表示持有股票
  //dp[3][2][1] 表示第三天 剩余交易次数为 2, 并且持有股票情况下的收益
  //状态转义方程:
  //dp[index][k][0] = Math.max(dp[index-1][k][0],dp[index-1][k][1] + prices[i]);
  //                             前一天没有持有       前一天持有, 今天卖出
  //dp[index][k][1] = Math.max(dp[index-1][k][1],dp[index-1][k-1][0] - prices[i]);
  //                             前一天持有           前一天不持有, 今天卖入
```
### 122-买卖股票的最佳时机 II
你可以尽可能地完成更多的交易（多次买卖一支股票）, 所以买卖次数的限制不需要了
```java
    //int[i+1][k][0/1] dp i: 表示第几天, 0 / 1: 0表示没有持有股票 1表示持有股票
    //dp[3][1] 表示第三天,并且持有股票情况下的收益
    //状态转义方程:
    //dp[index][0] = Math.max(dp[index-1][0],dp[index-1][1] + prices[i]);
    //                        前一天没有持有       前一天持有, 今天卖出
    //dp[index][1] = Math.max(dp[index-1][1],dp[index-1][0] - prices[i]);
    //                        前一天持有          前一天不持有, 今天卖入
```

### 309-最佳买卖股票时机含冷冻期
你可以尽可能地完成更多的交易（多次买卖一支股票)  
卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。  
```java
    //int[i+1][k][0/1] dp i: 表示第几天, 0 / 1: 0表示没有持有股票 1表示持有股票
    //dp[3][1] 表示第三天,并且持有股票情况下的收益
    //状态转义方程:
    //dp[index][0] = Math.max(dp[index-1][0],dp[index-1][1] + prices[i]);
    //                        前一天没有持有       前一天持有, 今天卖出
    //dp[index][1] = Math.max(dp[index-1][1],dp[index-2][0] - prices[i]);
    //                        前一天持有          前一天不持有, 今天卖入
```
### 714-买卖股票的最佳时机含手续费
你可以尽可能地完成更多的交易（多次买卖一支股票)  
你每笔交易都需要付手续费  
```java
    //int[i+1][k][0/1] dp i: 表示第几天, 0 / 1: 0表示没有持有股票 1表示持有股票
    //dp[3][1] 表示第三天,并且持有股票情况下的收益
    //状态转义方程:
    //dp[index][0] = Math.max(dp[index-1][0],dp[index-1][1] + prices[i]);
    //                        前一天没有持有       前一天持有, 今天卖出
    //dp[index][1] = Math.max(dp[index-1][1],dp[index-1][0] - prices[i] - fee);
    //                        前一天持有          前一天不持有, 今天卖入
```
[每日一题-188-买卖股票得最佳时机 IV.md](./每日一题-188-买卖股票得最佳时机 IV.md)  

### 每日一题-773-滑动谜题
#### 方法二: BFS 优化
使用字符串开销会比较大, 该用数组
```java
    int[][] directions = {{1, 3},{0, 2, 4},{1, 5},{0, 4},{1, 3, 5},{2, 4}};
    int row = 2;
    int col = 3;
    int length = 6;
    public int slidingPuzzle(int[][] board) {
        //使用数组, 字符串开销很大, 并且记录0的位置

        int[] end = {1,2,3,4,5,0};
        SlidingNode startNode = new SlidingNode(board);
        Queue<SlidingNode> queue = new LinkedList<>();
        queue.add(startNode);
        Set<SlidingNode> visited = new HashSet<>();
        visited.add(startNode);

        int step = 0;
        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                SlidingNode node = queue.poll();
                if (Arrays.equals(node.state,end)) { return step;}

                for (int di: directions[node.zore]) {                  
                    SlidingNode newNode = new SlidingNode(furtherState(node.state, di, node.zore),di);
                    if (!visited.contains(newNode)) {
                        queue.offer(newNode);
                        visited.add(newNode);
                    }
                }
            }
            ++step;
        }
        return -1;
    }
    private int[] furtherState(int[] state, int s,int t) {
        int[] ints = Arrays.copyOf(state, state.length);
        swap(ints,s,t);
        return ints;
    }

    private void swap(int[] state,int s, int t) {
        int temp = state[s];
        state[s] = state[t];
        state[t] = temp;
    }

    class SlidingNode {
        int[] state;
        int zore;
        public SlidingNode(int[][] board) {
            state = new int[length];
            for (int i = 0; i < length; i++) {
                state[i] = board[i / col][i % col];
                if (state[i] == 0) { zore = i; }
            }
        }

        public SlidingNode(int[] cur, int zore) {
            this.state = cur;
            this.zore = zore;
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;

            SlidingNode that = (SlidingNode) o;

            if (zore != that.zore) return false;
            return Arrays.equals(state, that.state);

        }

        @Override
        public int hashCode() {
            int result = Arrays.hashCode(state);
            result = 31 * result + zore;
            return result;
        }
    }
```
### 复杂度
* 时间复杂度: O(N)
* 空间复杂度: O(N)

#### 方法三: A*
```java
    int[][] directions = {{1, 3},{0, 2, 4},{1, 5},{0, 4},{1, 3, 5},{2, 4}};
    int[][] end = {{0, 0},{0, 1},{0,2},{1,9},{1, 1},{1,2}};
    int[] endState = {1,2,3,4,5,0};
    int row = 2;
    int col = 3;
    int length = 6;
    public int slidingPuzzle(int[][] board) {
        PuzzleNode startNode = new PuzzleNode(board);
        PriorityQueue<PuzzleNode> queue = new PriorityQueue<>();
        queue.offer(startNode);

        Set<PuzzleNode> visited = new HashSet<>();
        visited.add(startNode);

        while (!queue.isEmpty()) {
            PuzzleNode node = queue.poll();
            if (Arrays.equals(node.state,endState)) {
                return node.step;
            }

            for (int di: directions[node.zore]) {
                PuzzleNode newNode = new PuzzleNode(furtherState(node.state,di,node.zore),di,node.step+1);
                if (!visited.contains(newNode)) {
                    queue.offer(newNode);
                    visited.add(newNode);
                }
            }
        }
        return -1;
    }

    private int[] furtherState(int[] state, int s,int t) {
        int[] ints = Arrays.copyOf(state, state.length);
        swap(ints,s,t);
        return ints;
    }

    private void swap(int[] state,int s, int t) {
        int temp = state[s];
        state[s] = state[t];
        state[t] = temp;
    }
    class PuzzleNode implements Comparable<PuzzleNode>{
        int[] state;
        int zore;
        int priority;
        int step;

        public PuzzleNode(int[][] board) {
            state = new int[length];
            for (int i = 0; i < length; i++) {
                state[i] = board[i / col][i % col];
                if (state[i] == 0) {
                    zore = i;
                }
            }
            this.step = 0;
            this.priority = this.step + manhatten(state,endState);
        }

        public PuzzleNode(int[] cur, int zore,int step) {
            this.state = cur;
            this.zore = zore;
            this.step = step;
            this.priority = this.step + manhatten(state,endState);
        }

        private int manhatten(int[] curState,int[] endState) {
            int distance = 0;
            for (int i = 0; i < curState.length; i++) {
                int endIndex = curState[i]-1 < 0 ? 5 : curState[i]-1;
                int e = endState[endIndex];
                distance = Math.abs(e / col - end[i][0]) +  Math.abs(e / col - end[i][1]);
            }
            return distance;
        }

        @Override
        public int compareTo(PuzzleNode o) {
            return this.priority - o.priority;
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;

            PuzzleNode that = (PuzzleNode) o;

            if (zore != that.zore) return false;
            return Arrays.equals(state, that.state);

        }

        @Override
        public int hashCode() {
            int result = Arrays.hashCode(state);
            result = 31 * result + zore;
            return result;
        }
    }
```
### 复杂度
* 时间复杂度: O(N)
* 空间复杂度: O(N)

## 字符串匹配

### leetcode-刷题
1. [1143-最长公共子序列](https://leetcode-cn.com/problems/longest-common-subsequence/)
2. [72-编辑距离](https://leetcode-cn.com/problems/edit-distance/)
3. [115-不同得子序列](https://leetcode-cn.com/problems/distinct-subsequences/submissions/)
4. [44-通配符匹配](https://leetcode-cn.com/problems/wildcard-matching/)