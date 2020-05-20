# 230. Kth Smallest Element in a BST (Medium)

Given a *binary search tree*, write a function `kthSmallest` to find the **k**th smallest element.

- Example 1:
> **Input**: root = [5,3,6,2,4,null,null,1], k = 3 <br>
> **Output**: 3

         5
        / \
       3   6
      / \
     2   4
    /
    1

**Follow up**:
- What if the BST is modified (insert/delete operations) often and you need to find the kth smallest
  frequently? How would you optimize the kthSmallest routine?
  - Use a `double linked-list` for *O(k)* lookups. The list is a *serialization* of the tree, so for
    each insert/delete in the BST we insert/remove the node from the DDL. We'd need also a *map* for
    keeping references of the nodes in the BST and DDL

![BST with DDL](https://leetcode.com/problems/kth-smallest-element-in-a-bst/Figures/230/linked_list2.png)

## Brute Force
Serialize the BST into an array and then return the **k**th element.

**Time: O(n) <br> Space: O(n)**

## Optimal
We can do an **In Order** traversal, either *recursively* or *iteratively*. After processing the
left children of a node we decrease the *k* value. When *k == 0* we've found the **k**th element.

**Time: O(log n + k) <br> Space: O(log n + k)**

### Go solution
```go
func kthSmallest(root *TreeNode, k int) int {
    val := 0
    inOrder(root, &k, &val)
    return val
}

func inOrder(root *TreeNode, k, val *int) {
    if root == nil { 
        return 
    }
    
    inOrder(root.Left, k, val)
    
    *k--
    if *k == 0 {
        *val = root.Val
        return
    }
   
    inOrder(root.Right, k, val)
}
```
### Java solution
```java
class Solution {
    public int kthSmallest(TreeNode root, int k) {
        Stack<TreeNode> stack = new Stack();
        while(!stack.empty() || root != null) {
            while(root != null) {
                stack.push(root);
                root = root.left;
            }
            
            root = stack.pop();
            k--;
            if(k == 0) {
                return root.val;
            }
            root = root.right;
        }
        return -1;
    }
}
```