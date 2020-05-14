# 1325. Delete Leaves With a Given Value (Medium)

Given a binary tree `root` and an integer `target`, delete all the **leaf nodes** with value target.

Note that once you delete a leaf node with value target, if it's parent node becomes a leaf node an
d has the value target, it should also be deleted (you need to continue doing that until you can't).

- Example 1:
> **Input**: root = [1,2,3,2,null,2,4], target = 2 <br>
> **Output**: [1,null,3,null,4]

![alt tree1](https://assets.leetcode.com/uploads/2020/01/09/sample_1_1684.png)

- Example 2:
> **Input**: root = [1,3,3,3,2], target = 3 <br>
> **Output**: [1,3,null,null,2]

![alt tree2](https://assets.leetcode.com/uploads/2020/01/09/sample_2_1684.png)

## Optimal
For a given *node* we have to delete it's *children* if they're equal to target, then if
*node == target* we need to delete *node* as well. This is a **PreOrder** traversal. First we
process the children and then, we check if *node* is equal to target and if it became a leaf.

**Time: O(n) <br> Space: O(h)**

### Go solution
```go
func removeLeafNodes(root *TreeNode, target int) *TreeNode {
    return preOrder(root, target)
}

func preOrder(root *TreeNode, target int) *TreeNode {
    if root == nil {
        return nil
    }
    
    root.Left = preOrder(root.Left, target)
    root.Right = preOrder(root.Right, target)
    
    if root.Val == target && root.Left == nil && root.Right == nil {
        return nil
    }
    return root
}
```
### Java solution
```java
class Solution {
    public TreeNode removeLeafNodes(TreeNode root, int target) {
        return postOrder(root, target);
    }
    
    private TreeNode postOrder(TreeNode root, int target) {
        if(root == null)
            return null;
        
        root.left = postOrder(root.left, target);
        root.right = postOrder(root.right, target);
        
        if(root.val == target && root.left == null && root.right == null) {
            return null;
        }
        
        return root;
    }
}
```