[TOC]

# 学习笔记

## 每日一题
### 每日一题-191-位1的个数
**汉明距离: ** 两个等长字符串之间的汉明距离是两个字符串对应位置的不同字符的个数。换句话说，它就是将一个字符串变换成另外一个字符串所需要替换的字符个数  
**计算汉明距离: ** 对两个字符串进行异或运算，并统计结果为1的个数，那么这个数就是汉明距离  
**汉明重量: ** 一串符号中非零符号的个数。因此它等同于同样长度的全零符号串的汉明距离.   
**计算汉明重量: **   
**方法一:**  
1. 计算出来的值i的二进制可以按每2个二进制位为一组进行分组，各组的十进制表示的就是该组的汉明重量。
2. 计算出来的值i的二进制可以按每4个二进制位为一组进行分组，各组的十进制表示的就是该组的汉明重量。
3. 计算出来的值i的二进制可以按每8个二进制位为一组进行分组，各组的十进制表示的就是该组的汉明重量。
4. i * (0x01010101)计算出汉明重量并记录在二进制的高八位，>>24语句则通过右移运算，将汉明重量移到最低八位，最后二进制对应的十进制数就是汉明重量。
```C
// 计算32位二进制的汉明重量
int32_t swar(int32_t i)
{    
    i = (i & 0x55555555) + ((i >> 1) & 0x55555555); //binary: 01010101
    i = (i & 0x33333333) + ((i >> 2) & 0x33333333); //binary: 00110011
    i = (i & 0x0F0F0F0F) + ((i >> 4) & 0x0F0F0F0F); //binary: 00001111
    i = (i * (0x01010101) >> 24);
    return i;
}
```
时间复杂度 - O(1)  

**方法二: **
如果已知大多数数据位是0的话，那么还有更快的算法  
```C
//This is better when most bits in x are 0
//It uses 3 arithmetic operations and one comparison/branch per "1" bit in x.
int popcount_4(uint64 x) {
    uint64 count;
    for (count=0; x; count++)
  		  //n的最后一位1, 在n-1之后会变为0
        //n->20-> 10100   n-1->19-> 10011
        x &= x-1;
    return count;
}
```
[汉明重量](https://baike.baidu.com/item/%E6%B1%89%E6%98%8E%E9%87%8D%E9%87%8F)  
[汉明距离](https://baike.baidu.com/item/%E6%B1%89%E6%98%8E%E8%B7%9D%E7%A6%BB)  

## 位运算
1. 判断一个整数是奇数还是偶数：将该数与 1 相与，结果为 0 则是偶数，结果为 1 则是奇数。
2. 判断第 n 位是否是 1：x & (1<<n)，结果为 1 说明是，为 0 说明不是。
3. 将第 n 位设为 1：y = x | (1<<n)
4. 将第 n 为设为 0：y = x & ~(1<<n)
5. 将第 n 位的值取反：y = x ^ (1<<n)
6. 将最右边的 1 设为 0：y = x & (x-1)
7. 分离最右边的 1：y = x & (-x)
8. 将最右边的 1 后面都设位 1：y = x | (x-1)
9. 分离最右边的 0：y = ~x & (x+1)
10. 将最右边的 0 设为 1：y = x | (x+1)

[01-位运算 .md](./01-位运算 .md)

## 布隆过滤器
布隆过滤器, 可以确认某元素一定不在集合中, 和某元素可能在集合中  
布隆过滤器, 有一定的误差  
1. 如果过滤器返回某元素在集合中, 那么只是可能在  
2. 如果过滤器返回某元素不在集合中, 那么就肯定不在

### 代码实现
```java

```

## LRU缓存机制
由于计算机CPU的缓存容量有限, 所以在选择保留哪些数据, 删除哪些数据的时候, 需要一定的策略. 
其中一个策略叫: **LRU**  (stands for Least Recently Used), `最近`用过的数据就是 `有用的` , `很久`之前用过的 就是内存满的时候, 最先删除的 `无用的`数据.   
另外一个策略叫: **LFU**  (stands for Least Frequency Used), 我们干活的时候尝尝会把经常用的工具放在手边, 那么就有了LFU缓存机制  
### 下面主要讲解LRU  
**LRU数据结构** Hash表 + 双向链表
1. 利用的hashmap的快速查询 (但是hashmap是无序的)
2. 双向链表的 有序 和 快速删除 快速插入 (无法快速查找)
3. 这俩个数据结构正好互补

### 代码实现
```java
class LRUCache {
    //使用hashmap + 双向链表
    //利用的hashmap的快速查询 , 双向链表的 有序 和 快速删除 快速插入
    Map<Integer, MyDequeLinkedNode> map;
    MyDequeLinkedList container;
    public LRUCache(int capacity) {
        map = new HashMap<>();
        container = new MyDequeLinkedList(capacity);
    }
    
    public int get(int key) {
        //如果不存在, 返回-1
        //如果存在返回数字, 并移动到头部
        if (!map.containsKey(key)) {
            return -1;
        } else {
            int val = map.get(key).val;
            put(key,val);
            return val;
        }
    }
    
    public void put(int key, int value) {
        //如果存在key, 则删除原key, 放入头部
        //如果不存在key, 1.容器已满,删除某位,放入头部 2. 容器未满直接放入头部
        MyDequeLinkedNode node = new MyDequeLinkedNode(key,value);
        if (map.containsKey(key)) {
            container.remove(map.get(key));
            container.insertFirst(node);
            map.put(key,node);
        } else {
            if (container.isFull()) {
                MyDequeLinkedNode last = container.removeLast();
                map.remove(last.key);
            } 
            container.insertFirst(node);
            map.put(key,node);
        }
    }
}
```
## 排序
### N * N
#### 冒泡
 > 冒泡排序只会操作相邻的两个数据。每次冒泡操作都会对相邻的两个元素进行比较，看是否满足大小关系要求。如果不满足就让它俩互换。一次冒泡会让至少一个元素移动到它应该在的位置，重复 n 次，就完成了 n 个数据的排序工作。

```java
 public void bbSort(int[] a, int n) {
    if (a.length <= 1) {
        return;
    }

    //i < n, 冒泡排序在最坏得情况下, 需要对比n次
    for (int i = 0; i < n; ++i) {
        boolean flag = false;
        //j < n - i -1, j表示第j次对比得扫描指针
        for (int j = 0; j < n-i-1; ++j) {
            //找到最大得
            if (a[j] > a[j+1]) {
                int temp = a[j];
                a[j] = a[j+1];
                a[j+1] = temp;

                flag = true;
            }
        }

        //如果一趟遍历下来, 没有发生交换则说明数组中得数据已经排列完毕
        if (!flag) {
            break;
        }
    }
}
```

#### 插入
> 插入算法得思想是将数组分为 `已排区间` 和 `未排区间`, 初始得时候已排区间只有一个元素, 提取未排区间得元素于已排区间的元素从后往前对比, 在保证已排区间元素不乱得情况下,将提取得元素放到最合适得位置, 重复这个过程, 直到未排区间为空

```java
public void insertSort(int[] arrs,int n) {
    if (arrs.length <= 1) {
        return;
    }

    //i 表示未排区间得第一位元素
    for (int i = 1; i < n; ++i) {
        //将元素取出来
        int value = arrs[i];
        //从后向前比较
        int j = i - 1;
        //将value放在最合适得位置上, 当此次循环结束得时候, 就是j+1得位置就是value最合适得位置
        for (;j >= 0; --j) {
            if (arrs[j] > value) {
                arrs[j+1] = arrs[j];
            } else {
                break;
            }
        }
        arrs[j + 1] = value;
    }
}
```

#### 选择
最小的k个数, 选择排序
```java
    public int[] getLeastNumbers2(int[] arr, int k) {
        //3,2,1 每次对比处最小的一位放在第一位, 从后往前对比
        for (int i = 0; i < k; ++i) {
            for (int index = arr.length-1; index > i; --index) {
                if (arr[index] < arr[index-1]) {
                    int temp = arr[index];
                    arr[index] = arr[index-1];
                    arr[index-1] = temp;
                }
            }
        }
        return Arrays.copyOf(arr, k);
    }
```

### N * logN
#### merge sort
```java
数组arr[], 长度为n, 取中间值 q = (p + r) / 2
递归公式: merge_sort(p, r) = merge(merge_sort(p,q), merge_sort(q+1,r))
递归终止条件: q >= r
```

算法实现  
```java
public void mergeSort(int[] arrs, int n) {
    mergeSortC(arrs,0,n-1);
}

public void mergeSortC(int[] arrs, int start,int end) {
    //已经分化到每个区间只有一个得情况下了
    if (start >= end) {
        return;
    }

    int q = (start + end) / 2;
    //分化
    mergeSortC(arrs,start,q);
    mergeSortC(arrs,q+1,end);

    //合并
     mergeTogether(arrs,start,q,q+1,end);
}

public void mergeTogether(int[] arrs, int leftStart, int leftEnd, int rightStart, int rightEnd) {
    int[] temp = new int[rightEnd - leftStart + 1];
    
    //对比俩个数组, 选择较小得放入临时数组
    int i = leftStart;
    int j = rightStart;
    int k = 0;
    while (i <= leftEnd && j <= rightEnd) {
        if (arrs[i] < arrs[j]) {
            temp[k++] = arrs[i++];
        } else  {
            temp[k++] = arrs[j++];
        }
    }
		
		//合并剩下得
    int s;
    int e;
    if (i <= leftEnd) {
        s = i;
        e = leftEnd;
    } else {
        s = j;
        e = rightEnd;
    }

    for (; s <= e; ++s) {
        temp[k++] = arrs[s];
    }
		
		//放入到原数组中
    for (k = 0;k < temp.length; ++k) {
        arrs[k+leftStart] = temp[k];
    }
}
```
#### quick sort
```java
public void quickSort(int[] arrs,int n) {
    quickSortC(arrs,0,n-1);
}

private void quickSortC(int[] arrs,int start,int end) {
    //默认 取数组最后一个元素pivot, 并且遍历数组,将该元素定位到排序完成之后得正确位置--q
    //按照q左右俩側划分数组, q左边得都是小于pivot得, 右边都是大于pivot得
    //如此递归,一直到每个分区只有一个数组
    if (start >= end) {
        return;
    }

    //定位最正确得位置
    int q = quickDivide(arrs,start,end);

    //递归得方式继续分化
    quickSortC(arrs,start,q-1);
    quickSortC(arrs,q+1,end);
}

private int quickDivide(int[] arrs, int start,int end) {
    //将qivot放到正确得位置
    int qivot = arrs[end];
    //i用于定位qivot最合适得位置
    int i = start;
    //j 用于遍历整个数组, 最后一个不用遍历
    for (int j = start; j <= end-1; ++j) {
        //将小于qivot得元素放在左边, 大于qivot得放在右边
        if (arrs[j] < qivot) {
            int temp = arrs[j];
            arrs[j] = arrs[i];
            arrs[i] = temp;
            ++i;
        }
    }

    //所以快排并不是稳定得算法
    int temp = arrs[i];
    arrs[i] = qivot;
    arrs[end] = temp;

    return i;
}
```

## 实战题目/参考链接
https://leetcode-cn.com/problems/design-a-leaderboard/  
[十大经典排序算法](https://www.cnblogs.com/onepixel/p/7674659.html) 
[9种经典排序算法可视化动画](https://www.bilibili.com/video/av25136272)
[6分钟看完15种排序算法动画展示](https://www.bilibili.com/video/av63851336) 