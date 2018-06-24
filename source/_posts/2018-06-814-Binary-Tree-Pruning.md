---
title: 814.Binary Tree Pruning
description: 二叉树的剪枝：给定一个二叉树，节点的值要么是0、要么是1。要求把所有节点都为0的子树剪掉。
categories: LeetCode
tags:
  - LeetCode
  - Binary Tree
date: 2018-06-24 20:17:41
updated: 2018-06-24 20:17:41
---


[LeetCode链接：814.Binary Tree Pruning](https://leetcode.com/problems/binary-tree-pruning/)
# 题目
给定一个二叉树，节点的值要么是0、要么是1。
要求把所有节点都为0的子树剪掉。

# 举例
## Example 1:
> Input: [1,null,0,0,1]
> Output: [1,null,0,null,1]
> 
> Explanation: 
> Only the red nodes satisfy the property "every subtree not containing a 1".
> The diagram on the right represents the answer.
> ![](1028_2.png)

## Example 2:
> Input: [1,0,1,0,0,0,1]
> Output: [1,null,1,null,1]
> ![](1028_1.png)

## 分析
根据题意，如果某个子树的『和』等于0，就可以将其剪掉（node.left=None或者node.right=Node）.

求子树的『和』，可以使用**后序遍历**。

除了记录子树的『和』之外，还需要记录子树的『节点个数』。只有同时满足『和』=0且『节点个数』>0时，才将其剪掉。

因此，用两个值`[sum, num]`分别记录以当前节点为根节点的子树的『和』与『节点个数』。
这两个值的更新如下：
```python
sum = left_sum + right_sum + node.val
num = left_num + right_num + 1
```

## 代码
Python代码如下：
```python
def pruneTree(self, root):
    """
    :type root: TreeNode
    :rtype: TreeNode
    """
    def helper(node):
        if not node:
            return [0, 0] 
        
        # 后序遍历，计算[和，节点个数]
        [left_sum, left_num] = helper(node.left)
        [right_sum, right_num] = helper(node.right)
        sum = left_sum + right_sum + node.val
        num = left_num + right_num + 1
        
        # 判断并剪枝
        # 如果左子树『节点个数』> 0 且 『和』= 0
        if left_num > 0 and left_sum == 0: 
            node.left = None
        # 如果又子树『节点个数』> 0 且 『和』= 0
        if right_num > 0 and right_sum == 0: 
            node.right = None

        # 返回：以当前节点为根节点的子树的『和』与『节点个数』
        return [sum, num]
    helper(root)
    return root        
```