# 144. Binary Tree Preorder Traversal

Given a binary tree, return the **preorder** traversal. Recursive solution is trivial, do it
**iteratively**.

- Example 1:
> **Input**: [10,5,13,null,8,2,99] <br>
> **Output**: [10,5,null,8,13,2,99]

       10
      /   \
      5   13
       \  / \
        8 2  99

## Brute Force
Use a recursive function.

**Time: O(n) <br> Space: O(h)** (h = height of the tree, may become *n* for a skewed tree)

## Optimal
Use a **stack**. Push the *current node* to the stack and add the value to the *output array*. Loop
over the left children. Once there is no left child, pop an element of the stack and repeat the
process for its right child.

**Time: O(n) <br> Space: O(h)**

### Go solution
```go
func preorderTraversal(root *TreeNode) []int {
    ans := make([]int, 0, 10)
    stack := make([]*TreeNode, 0, 10) // Use slice as stack
    
    for len(stack) > 0 || root != nil {
        for root != nil {
            ans = append(ans, root.Val)
            stack = append(stack, root)
            root = root.Left
        }
        root = stack[len(stack) - 1]
        stack = stack[:len(stack) - 1]
        root = root.Right
    }
    
    return ans
}
```
### Java solution
```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> ans = new ArrayList();
        Stack<TreeNode> stack = new Stack();
        TreeNode curr = root;
        
        while(!stack.empty() || curr != null) {
            while(curr != null) {
                ans.add(curr.val);
                stack.push(curr);
                curr = curr.left;
            }
            curr = stack.pop();
            curr = curr.right;
        }
        
        return ans;
    }
}
```