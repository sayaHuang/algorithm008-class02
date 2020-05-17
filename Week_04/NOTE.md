[TOC]

# 学习笔记

## 每日一题
### 每日一题-33-搜索旋转排序数组
1. 给的数组排好序之后, 再未知点做了旋转
2. 算法时间复杂度必须是 O(log n) 级别
3. 没有找到目标返回-1
4. 并没有说数组不为空
5. 并且数组中没有重复的元素

#### 方法一:二分法

中间点取, 中间偏右

```java
    //使用二分查找
    //1,3,5,6,9,11,14,16,17,23
    //发生旋转
    //情况1: 10,11,14,16,17,23,1,3,5,6,9  mid = 23  mid > right , left有序(数组的元素个数为奇数)
    //                      ^
    //情况1: 10,11,14,16,17,0,1,3,5,6,9  mid = 0  mid < right , right有序(数组的元素个数为奇数)
    //                     ^
    //情况2: 11,14,16,17,23,1,3,5,6,9  mid = 1,  mid < right , right有序(数组的元素个数为偶数)
    //                     ^
    //情况2: 11,14,16,17,23,24,3,5,6,9  mid = 24,  mid > right , left有序(数组的元素个数为偶数)
    //                      ^
    //情况3: 6,9,10,11,14,16,17,23,1,3,5  mid = 16  mid > right , left有序
    //                    ^
    //情况4: 16,17,23,1,3,5,6,9,11,14,15  mid = 5  mid < right , right有序
    //                   ^
    public int search(int[] nums, int target) {
        if (nums.length == 0) {
            return -1;
        }
        int left = 0; int right = nums.length-1;
        while (left < right) {
            //int mid = left + (right-left+1) / 2 这种方式可以在数组个数是偶数的时候取中偏右
            //当区间只有俩个元素的时候, 取中偏右
            //0 1 2 3, left = 0, right = 3, mid = 2
            //0 1 2 , left = 0, right = 2, mid = 1
            int mid = left + (right-left+1) / 2;
            if (nums[mid] < nums[right]) {
                //[mid,right] 有序
                if (target >= nums[mid] && target <= nums[right]) {
                    left = mid;
                } else {
                    right = mid - 1;
                }

            } else {
                //[left,mid-1] 有序, -1是为了收缩数据区间
                if (target >= nums[left] && target <= nums[mid-1]) {
                    right = mid - 1;
                } else {
                    left = mid;
                }
            }
        }
        //当区间中只有一个元素的时候, left == right, 就会跳出while循环
        //虽然left == right了, 但是数据不一定就是被定位left(或right)
        //16,17,23,1,3,5,6,9,11,14,15, target = 12,  最后left=right= 8, 但是nums[8] = 11
        if (nums[left] == target) {
            return left;
        }
        return -1;
    }
```
就 `[11,14] target = 12, first mid = 14`问: 为什么 right = mid - 1
答: 假如right = mid
1. 由于 mid = end = 1 , 所以会走到 `if target >= nums[left] && target <= nums[mid-1]`
2. 由于 start = mid-1 = 0. 并且 12 > 11, 会触发赋值 `right = mid`
3. 不难发现 这是一个死循环
4. 由于mid得值再偶数得情况下有中间偏左和中间偏右, 咱们去的是中间偏右, 所以再偶数得时候, end=mid-1, start=mid, 可以从中间一撇俩半


#### 复杂度
* 时间复杂度: O(logN)
* 空间复杂度: O(1)

#### 方法二: 中间偏左

```java
    //情况1: 10,11,14,16,17,23,1,3,5,6,9  mid = 23  mid > right , left有序(数组的元素个数为奇数)
    //                      ^
    //情况1: 10,11,14,16,17,0,1,3,5,6,9  mid = 0  mid < right , right有序(数组的元素个数为奇数)
    //                     ^
    //情况2: 11,14,16,17,23,1,3,5,6,9  mid = 23,  mid > right , left有序(数组的元素个数为偶数)
    //                   ^
    //情况2: 11,14,16,17, 0,1,3,5,6,9  mid = 0,  mid < right , right有序(数组的元素个数为偶数)
    //                   ^
    //情况3: 6,9,10,11,14,16,17,23,1,3,5  mid = 16  mid > right , left有序
    //                    ^
    //情况4: 16,17,23,1,3,5,6,9,11,14,15  mid = 5  mid < right , right有序
    //                   ^
    public int search(int[] nums, int target) {
        if (nums.length == 0) {
            return -1;
        }
        int left = 0; int right = nums.length-1;
        while (left < right) {
            //int mid = left + (right-left+1) / 2 这种方式可以在数组个数是偶数的时候取中偏右
            //当区间只有俩个元素的时候, 取中偏左
            //0 1 2 3, left = 0, right = 3, mid = 2
            //0 1 2 , left = 0, right = 2, mid = 1
            int mid = left + (right-left) / 2;
            if (nums[mid] < nums[right]) {
                //[mid+1,right] 有序
                if (target >= nums[mid+1] && target <= nums[right]) {
                    left = mid + 1;
                } else {
                    right = mid;
                }

            } else {
                //[left,mid] 有序, -1是为了收缩数据区间
                if (target >= nums[left] && target <= nums[mid]) {
                    right = mid;
                } else {
                    left = mid + 1;
                }
            }
        }
        //当区间中只有一个元素的时候, left == right, 就会跳出while循环
        //虽然left == right了, 但是数据不一定就是被定位left(或right)
        //16,17,23,1,3,5,6,9,11,14,15, target = 12,  最后left=right= 8, 但是nums[8] = 11
        if (nums[left] == target) {
            return left;
        }
        return -1;
    }
```
#### 总结
1. 中间点得选择
 * 对比俩个例子发现, 中间点得选择, 确定了了怎么划分原数组
 * 终点取左, `int mid = left + (right-left) / 2;`` [left,mid] [mid+1,right]`
 * 终点取右, `int mid = left + (right-left+1) / 2;`` [left,mid-1] [mid,right]`
2. 分割区间得确认, 同样影响单调区间中确认target是否再单调区间得对比代码
 * 方法一: `//[mid,right] 有序 if (target >= nums[mid] && target <= nums[right]) {}`
 * 方法二: `//[mid+1,right] 有序 if (target >= nums[mid+1] && target <= nums[right]) {}`  
[每日一题-33-搜索旋转排序数组.md](./每日一题-33-搜索旋转排序数组.md)

### 每日一题-69-x的平方根
**可以使用二分查找**
1. 由于是求正解, 所以 y=x^2^, 再x轴的左半边是单调递增的
2. 并且解的范围是再 [0,x]之间, 考虑 0 1 2的平方是 0 1 4
3. 并且可以使用left < right确认左右边界, 直到left == right, left就是我们要找的值, (因为每次都是+1,-1所以最终left==right, 跳出循环)

**也可以使用牛顿迭代法**


## BFS & DFS

## 贪心思想分析
1. 针对一组数据, 有`限制值`和`期望值`, 在满足限制的情况下, 期望值最大. 限制在50kg的装载范围内, 总价值最大
2. 贪心算法 并不总能给出最优解 
 *  找零问题, 硬币面值为100,99,1, 仅数量每种各100张, 需要找零396元, 期望使用的硬币最少
 *  贪心算法先用100, 在用99, 在用1, 最后得 使用3张100元, 96个1元
 *  其实最优解是使用4个99元
 *  贪心思想是每一次选择, 都选择最优的, 但是之前的选择会限制后面的选择, 从而无法达到全局最优
 *  类似人生路口的各种选项, 每次都做出当前情况的最优, 但是不一定可以达到最优的人生, 人生最优的评价标准又是什么呢, 是社会公认的价值观, 但是到达了之后, 又真的是最优的么
3. 什么样的问题适合贪心思想
 * 每次选择最优的解, 对期望值贡献也是最大的
 * 找零问题, 面值为 100 10 1, 仅数量每种各100张, 需要找零396元, 期望使用的硬币最少
 * 贪心算法先用100, 在用10, 在用1, 最后得 使用3张100元, 9个10元, 6个1元
 * 不难发现这个就是最优解, 因为选择 10个10元 , 就是选1个100元  
 [更多](./02-贪心.md)

## 二分查找
### 二分查找要素
1. **有序** 查找的对象是单调的
2. **有限** 查找对象有上下边界
3. **随机访问任意元素** 查找的对象支持下标快速访问指定元素, 例如数组O(1), 跳表O(logN)

### 二分代码模板
```java
    public int search(int[] nums, int target) {
        int start = 0; int end = nums.length-1;
        while (start <= end) {
            int mid = start + (end - start + 1) / 2;
            if (nums[mid] == target) {
                return mid;
            } 
            if (target > nums[mid]) {
                start = mid + 1;
            } else {
                end = mid - 1;
            }
        }
        return -1;
    }
```