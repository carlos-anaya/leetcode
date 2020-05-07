# 1008. Construct Binary Search Tree from Preorder Traversal

Return the root node of a *binary search tree* that matches the given *preorder traversal*.

(Recall that a binary search tree is a binary tree where for every node, any descendant of 
node.left has a value < node.val, and any descendant of node.right has a value > node.val.  Also 
recall that a preorder traversal displays the value of the node first, then traverses node.left, 
then traverses node.right.)

- Example:
> **Input**: [8,5,1,7,10,12] <br>
> **Output**: [8,5,10,1,7,null,12] <br>
> **Explanation**: See the tree below

![alt binary search tree](https://assets.leetcode.com/uploads/2019/03/06/1266.png)

## Brute Force
Use a plain BST and insert each element of the input array.

**Time: O(n²) <br> Space: O(1)**

## Optimal
Let's consider the tree [8,5,10]. First we need to create a node for 8, then 5 has to be it's left
child and 10 it's right child.

We can follow an **in-order** traversal, creating a node for the current element and then checking
if the next element should be a left or right child. 

- But how do we know what is the current element? We can use an *instance variable* or a *pointer*
    to the current element. This way we ensure one pass.
- How do we know if it should be a left or right child? By using *min/max variables* we can control
    what child should it be: left child if value between min to curr.val and right child if value
    between curr.val to max.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func bstFromPreorder(preorder []int) *TreeNode {
    idx := 0
    return build(&preorder, &idx, math.MinInt32, math.MaxInt32)
}

func build(preorder *[]int, idx *int, min, max int) *TreeNode {
    if *idx >= len(*preorder) {
        return nil
    }
    
    var root *TreeNode
    val := (*preorder)[*idx]
    if val > min && val < max { // value is in range, so we create a node
        root = &TreeNode{Val: val}
        (*idx)++
        root.Left = build(preorder, idx, min, val) // check if next element is a left child
        root.Right = build(preorder, idx, val, max) // check if next element is a right child
    }
    
    return root
}
```
### Java solution
```java
class Solution {
    int idx;
    public TreeNode bstFromPreorder(int[] preorder) {
        idx = 0;
        return buildBST(preorder, Integer.MIN_VALUE, Integer.MAX_VALUE);
    }
    
    private TreeNode buildBST(int[] preorder, int min, int max) {
        if(idx >= preorder.length || preorder[idx] <= min || preorder[idx] >= max) {
            return null;
        }
        TreeNode node = new TreeNode();
        node.val = preorder[idx];
        idx++;
        node.left = buildBST(preorder, min, node.val);
        node.right = buildBST(preorder, node.val, max);
        
        return node;
    }
}
```