# 199. Binary Tree Right Side View (Medium)

Given a *binary tree*, imagine yourself standing on the *right* side of it, return the values of the
nodes you can see ordered from top to bottom.

- Example 1:
> **Input**: [1,2,3,4,5,6] <br>
> **Output**: [1,3,5,6]

         1    <---
        / \
       2   3  <---
      / \
      4  5    <---
     /
    6         <---

## Optimal
We use a `BFS` traversal since we're interested on the **right-most** node in each **level**. At
each level we first add the right child and then the left child, this way we ensure the first polled
node is the rightmost available. (can insert in regular order too, and the right most node is the
last node of the level).

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func rightSideView(root *TreeNode) []int {
    ans := make([]int, 0, 10)
    if root == nil {
        return ans
    }
    q := make([]*TreeNode, 0, 10)
    q = append(q, root)
    
    for len(q) > 0 {
        size := len(q)
        for i := 0; i < size; i++ {
            root = q[0]
            q = q[1:] // poll from queue
            if i == 0 { // rightmost element
                ans = append(ans, root.Val)
            }
            if root.Right != nil {
                q = append(q, root.Right)
            }
            if root.Left != nil {
                q = append(q, root.Left)
            }
        }
    }
    return ans
}
```
### Java solution
```java
class Solution {
    public List<Integer> rightSideView(TreeNode root) {
        List<Integer> ans = new ArrayList();
        if(root == null)
            return ans;
        // Use a BFS approach, take the first element of the queue per level as ans;
        Queue<TreeNode> q = new LinkedList();
        q.add(root);
        while(!q.isEmpty()) {
            int size = q.size();
            // poll all the elements of the level
            for(int i = 0; i < size; i++) {
                root = q.poll();
                if(i == size - 1) // add rightmost node.val per level to ans
                    ans.add(root.val);
                
                if(root.left != null)
                    q.add(root.left);
                if(root.right != null)
                    q.add(root.right);
                
            }
        }
        return ans;
    }
}
```