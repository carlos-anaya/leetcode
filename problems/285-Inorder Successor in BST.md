# 285. Inorder Successor in BST (Medium)

Given a **binary search tree** and a node **p** in it, find the **in-order successor** of that node
in the BST.

The successor of a node `p` is the node with the **smallest key greater** than `p.val`.

- Example 1:
> **Input**: root = [5,3,6,2,4,null,null,1], p = 6 <br>
> **Output**: null
- Example 2:
> **Input**: root = [5,3,6,2,4,null,null,1], p = 3 <br>
> **Output**: 4
- Example 2:
> **Input**: root = [5,3,6,2,4,null,null,1], p = 4 <br>
> **Output**: 5

![tree](https://assets.leetcode.com/uploads/2019/01/23/285_example_2.PNG)

## Brute Force
In-order traversal of the tree to an array, then find `p` next value.

**Time: O(n) <br> Space: O(n)**

## Optimal
We can take advantage of the BST property and decide to move right or left. When we find a node
greater than `p` this is an *candidate*. If the candidate has a left branch, then we traverse it and
return the result (which will be the *left-most* node). Otherwise, return the *candidate*. If we
find a node smaller or equal to `p`, then we move right.

**Time: O(log\*n) <br> Space: O(1)**

### Go solution
```go
func inorderSuccessor(root *TreeNode, p *TreeNode) *TreeNode {
    if root == nil {
        return nil
    }
    // current node is smaller or equal to target, so inOrder successor
    // must be in the right branch
    if root.Val <= p.Val {
        return inorderSuccessor(root.Right, p)
    }
    // current node is larger than target, so inOrder successor
    // must be in left branch or, if no left branch, the root is the successor
    left := inorderSuccessor(root.Left, p)
    if left != nil {
        return left
    }
    return root
}
```
### Java solution
```java
class Solution {
    public TreeNode inorderSuccessor(TreeNode root, TreeNode p) {
        TreeNode candidate = null;
        
        while(root != null) {
            if(root.val <= p.val) { // curr node is smaller than "p", so move right
                root = root.right;
            } else { // curr node is greater than "p", so it's a candidate
                candidate = root;
                root = root.left;
            }
        }
        return candidate;
    }
}
```