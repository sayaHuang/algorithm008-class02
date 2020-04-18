[TOC]

# 今日一题: 删除链表倒数第 n 个结点
## 需求
> 给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。

示例:   
```swift
给定一个链表: 1->2->3->4->5, 和 n = 2.
当删除了倒数第二个节点后，链表变为 1->2->3->5.
```
**说明:** 给定的n保证有效  
**进阶:** 你能尝试使用一趟扫描实现么?  

### 审题所得到的信息
* 给定一个链表, 默认为单链表
* 删除
* 倒数第n个结点
* 返回链表的头结点
* 给定的n保证有效, 则同理给定的链表也肯定有效

## 方法1:
要删除链表倒数第n位, 需要知道链表的长度L, 之后增加哨兵结点,  因为增加了头结点, 所以想要删除第n位的结点, 可以定位( L-n )个结点. 并删除该结点之后的结点.   

```Java
public class ListNode {
    int val;
    ListNode next;
    ListNode(int x) { val = x; }
}

class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {

        ListNode firstNode = head;
        int count = 0;

        //0. 添加哨兵结点
        ListNode guardNode = new ListNode(0);
        guardNode.next = head;

        //1. 计算链表的长度
        while (firstNode != null) {
            ++count; 
            firstNode = firstNode.next;
        }

        //2. 删除指定位置的数据
        firstNode = guardNode;
        count -= n;
        while (count > 0) {
            --count;
            firstNode = firstNode.next; 
        }
        firstNode.next = firstNode.next.next;
        return guardNode.next;
    }
}
```

### 分析时间复杂度
时间复杂度取决于链表的长度, 以及 n 的位置 . f(n) = L + L - n, 则时间复杂度为 O(n).  
空间复杂度为 O(1).  

## 方法2:
使用双指针方法  
1. 首先添加头结点
2. 如果需要删除倒数第n位的数据, 第一个指针先定位正数n+1个位置
3. 此时将头结点赋值给第二个指针, 保证俩个指针相差n+1个位置
4. 使用while循环, 将俩个结点同时向下遍历, 直到第一个结点遍历到尾部
5. 第二个结点则顺利遍历到倒数第n+1个位置, 删除该结点之后的一个结点

```swift
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {

        //0. 添加哨兵结点
        ListNode guardNode = new ListNode(0);
        guardNode.next = head;

        //1. 第一个指针定位n+1个位置
        int count = n + 1;
        ListNode firstIndex = guardNode;
        while (count > 0) {
            firstIndex = firstIndex.next;
            --count;
        }

        //2. 第二个指针保持跟第一个指针的间隔为N + 1
        ListNode secondIndex = guardNode;
        while (firstIndex != null) {
            firstIndex = firstIndex.next;
            secondIndex = secondIndex.next;
        }

        secondIndex.next = secondIndex.next.next;
        return guardNode.next;
    }
}
```

### 分析时间复杂度
第一个 结点从头结点一路遍历到尾结点, 时间复杂度位O(n)  
空间复杂度为 O(1).  

## 测试案例覆盖
数组长度是L  
1->2->3->4->5, 倒数n = 2.  
1->2->3->4->5, 倒数n = 1.  
1->2->3->4->5, 倒数n = L.  

1->2, 倒数n = 1.  
1->2, 倒数n = L.  
 
1, 倒数n = 1;  

## leetcode地址
[leetcode地址](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/submissions/)
