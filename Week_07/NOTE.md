[TOC]
# 学习笔记
## 每日一题
### 每日一题-547-朋友圈
二维数组很自然的可以联想到图 , 所以可以尝试使用 DFS  
根据题意 `已知 A 是 B 的朋友，B 是 C 的朋友，那么我们可以认为 A 也是 C 的朋友` 可以联想到使用并查集解决该问题
[每日一题-547-朋友圈.md](./每日一题-547-朋友圈.md)

## 并查集
解决图论中「动态连通性」问题的  
这里所说的「连通」是一种等价关系，也就是说具有如下三个性质：  
1、自反性：节点p和p是连通的。  
2、对称性：如果节点p和q连通，那么q和p也连通。  
3、传递性：如果节点p和q连通，q和r连通，那么p和r也连通。  

[labuladong](https://leetcode-cn.com/problems/friend-circles/solution/union-find-suan-fa-xiang-jie-by-labuladong/)