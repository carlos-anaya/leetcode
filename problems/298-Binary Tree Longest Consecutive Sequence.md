# 298. Binary Tree Longest Consecutive Sequence (Medium)

Given a `binary tree`, find the length of the **longest consecutive increasing sequence** path.

The path refers to any sequence of nodes from some starting node to any node in the tree along the
parent-child connections. The longest consecutive path need to be from parent to child (cannot be
the reverse).

- Example 1:
> **Input**: [1,null,3,2,4,null,null,null,5] <br>
> **Output**: 3 <br>
> **Explanation**: Longest consecutive sequence path is 3-4-5, so return 3.

    1
      \
       3
      / \
     2   4
          \
           5

## Brute Force
For each node traverse in a pre-order way finding the longest consecutive sequence.

**Time: O(n²) <br> Space: O(n)**

## Optimal
Do a `post-order` traversal. For left and child nodes check if they match the current's node value
plus one. Then if the current node's value is the parent node's value plus one, return 1 plus the
best of it's children values. If not, return 0 since it is not sequence of its parent. Use an
*instance variable* for keeping track of the longest consecutive sequence at each step.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
var ans int
func longestConsecutive(root *TreeNode) int {
    if root == nil { return 0 }
    ans = 0
    dfs(root, root.Val+1)
    return ans
}

func dfs(root *TreeNode, val int) int {
    if root == nil {
        return 0
    }
    
    // get longest sequence of children
    l := dfs(root.Left, root.Val + 1)
    r := dfs(root.Right, root.Val + 1)
    
    // the current subsequence is 1 plus the best to the left/right (if valid)
    ans = max(ans, 1 + max(l, r))
    
    // node is sequence of father
    if root.Val == val {
        return 1 + max(l, r)
    }
    return 0
}
```
### Java solution
```java
class Solution {
    int ans;
    public int longestConsecutive(TreeNode root) {
        ans = 0;
        if(root != null)
            preorder(root, root.val);
        return ans;
    }
    
    private int preorder(TreeNode root, int nextVal) {
        if(root == null)
            return 0;
        int l = preorder(root.left, root.val+1);
        int r = preorder(root.right, root.val+1);
        
        // a node has a sequence of 1 plus the best sequence
        // of its children (if any)
        ans = Math.max(ans, 1 + Math.max(l, r));
        
        // root matches the next val (sequence)
        // return 1 plus the sequence count of its children
        if(root.val == nextVal) {
            return 1 + Math.max(l, r);
        }
        // child didn't follow parent's sequence
        return 0;
    }
}
```