# 543. Diameter of Binary Tree

Given a binary tree, you need to compute the length of the diameter of the tree. 
The diameter of a binary tree is the length of the **longest** path between any two nodes in a tree. 
This path may or may not pass through the root.

- Example:
>          1    
>         / \   
>        2   3  
>       / \     
>      4   5    
> **Output**: 3 ( from 4 -> 2 -> 1 -> 3)

## Optimal
Use a `instance variable` (Java) or a `pointer variable` (Go) to track the max value.
1. Get the height of the left and right subtree of a node.
2. Check if the *"diameter"* of the current subtree is max, i.e. **ans = max(ans, l + r)**.
  No need to count current node (because max diameter could be in a subtree, without root).
3. Return the **max(l, r) + 1** (i.e., max height counting the actual node).

**Time: O(n)** (Visiting all the nodes)  <br> 
**Space: O(log n)** (Memory stack of the recursion)

### Go solution
```go
func diameterOfBinaryTree(root *TreeNode) int {
    ans := 0
    getHeight(root, &ans)
    return ans
}

func getHeight(root *TreeNode, ans *int) int {
    if root == nil {
        return 0
    }
    
    l := getHeight(root.Left, ans)
    r := getHeight(root.Right, ans)
    
    if l + r > *ans {
        *ans = l + r
    }
    
    if l > r {
        return l + 1
    }
    
    return r + 1
}
```
### Java solution
```java
class Solution {
    int ans; // Instance variable/field
    public int diameterOfBinaryTree(TreeNode root) {
        getHeight(root);
        
        return ans;
    }
    
    private int getHeight(TreeNode root) {
        if(root == null)
            return 0;
        
        int l = getHeight(root.left);
        int r = getHeight(root.right);
        
        ans = Math.max(ans, l + r);
        
        return Math.max(l, r) + 1;
    }
}
```