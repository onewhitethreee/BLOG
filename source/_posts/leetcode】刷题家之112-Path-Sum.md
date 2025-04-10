---
uuid: d795ec90-13c4-11f0-8cd6-3b0c89ef1167
title: 【leetcode】刷题家之112. Path Sum
date: 2025-04-07 17:27:39
tags: [leetcode]
categories: [leetcode]
---

最近在学swift，感觉这个就像是c++和python的合体。

为了熟悉一下这个语法，刷题都用这个来写好了。

# 请看题

![](https://img.164314.xyz/2025/04/82b1ab4f5bb007f93423550626ce9e5b.png)

![](https://img.164314.xyz/2025/04/c0749cd32e0924f8ea5a1aa055554f90.png)

# 解题思路

这道题给了我们一个targetSum，也就是每一个节点相加后的结果。要求找到这么一条路径，使其的值相加结果为targetSum。

那么很简单，每一次递归的时候传入targetSum - 当前的值，只要最终的结果为0，那么就一定能够找到那条路径，返回True即可。

有了这么个想法后就可以开始写代码了。

# 代码

```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     public var val: Int
 *     public var left: TreeNode?
 *     public var right: TreeNode?
 *     public init() { self.val = 0; self.left = nil; self.right = nil; }
 *     public init(_ val: Int) { self.val = val; self.left = nil; self.right = nil; }
 *     public init(_ val: Int, _ left: TreeNode?, _ right: TreeNode?) {
 *         self.val = val
 *         self.left = left
 *         self.right = right
 *     }
 * }
 */
class Solution {
    func hasPathSum(_ root: TreeNode?, _ targetSum: Int) -> Bool {
        if(root == nil){
            return false;
        }
        if(root?.left == nil && root?.right == nil){
            return root!.val == targetSum; // 这里指的是在最后一个节点，如果它的值刚好是targetSum，那么答案就是True，也就是一定有那条路径。
        }
        return hasPathSum(root?.left, targetSum - root!.val) || hasPathSum(root?.right, targetSum - root!.val);
    }
}
```