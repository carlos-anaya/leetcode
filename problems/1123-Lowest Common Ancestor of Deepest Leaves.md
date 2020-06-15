# 1123. Lowest Common Ancestor of Deepest Leaves (Medium)

Given a rooted binary tree, return the lowest common ancestor of its deepest leaves.

- Example 1:
> **Input**: root = [1,2,3] <br>
> **Output**: [1,2,3] <br>
> **Explanation**: Return the node 1, which is serialized to [1,2,3]
- Example 2:
> **Input**: root = [1,2,3,4] <br>
> **Output**: [4]
- Example 3:
> **Input**: root = [1,2,3,4,5] <br>
> **Output**: [2,4,5]

## Optimal
We do a `post-order traversal`. We keep track of the depth at each level and keep on updating the
*deepest* level. Once a node has children that are the deepest, this node is the 
*Lowest common ancestor*. 

**Time: O(n) <br> Space: O(n)**

### Java solution
```java
class Solution {
    TreeNode ancestor;
    int deepest;
    
    public TreeNode lcaDeepestLeaves(TreeNode root) {
        ancestor = null;
        deepest = 0;
        postorder(root, 0);
        return ancestor;
    }
    
    private int postorder(TreeNode root, int depth) {
        deepest = Math.max(deepest, depth);
        if(root == null)
            return depth;
        int left = postorder(root.left, depth + 1);
        int right = postorder(root.right, depth + 1);
        
        if(left == deepest && right == deepest) {
            ancestor = root;
        }
        // return the deeper child
        return Math.max(left, right);
    }
}
```