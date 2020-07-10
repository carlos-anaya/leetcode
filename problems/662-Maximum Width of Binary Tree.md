# 662. Maximum Width of Binary Tree (Medium)

Given a binary tree, write a function to get the **maximum width** of the given tree. The width of a
tree is the maximum width among all levels. The binary tree has the same structure as a full binary
tree, but some nodes are null.

The width of one level is defined as the length between the end-nodes (the leftmost and right most
non-null nodes in the level, where the null nodes between the end-nodes are also counted into the
length calculation.

- Example 1:
> **Input**:

           1
         /   \
        3     2
       / \     \  
      5   3     9 

> **Output**: 4 <br>
> **Explanation**: The maximum width existing in the third level with the length 4 (5,3,null,9).
- Example 2:
> **Input**:<br>

          1
         /
        3
       / \
      5   3

> **Output**: 2
- Example 3:
> **Input**:

          1
         / \
        3   2
       /     \  
      5       9 
     /         \
    6           7

> **Output**: 4 <br>
> **Explanation**: The maximum width existing in the fourth level with the length 8:
> (6,null,null,null,null,null,null,7).

## Optimal
We do a `bfs traversal` and keep track of the index of each node. Why does the index is useful? We
can get the first and last element of each level, and the difference of the indexes is the width of
that level. 

Recall how a tree is stored in an array. We start at index 0, and for each node, its left and right
children are located at *parentIdx\*2* and *parentIdx\*2+1*:

![alt tree](http://mishadoff.com/images/dfs/binary_tree_incomplete.png)

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
type Pair struct {
    node *TreeNode
    idx int
}

func widthOfBinaryTree(root *TreeNode) int {
    if root == nil {
        return 0
    }
    ans := 0
    q := make([]Pair, 0, 100)
    q = append(q, Pair{root,1})
    
    for len(q) > 0 {
        size := len(q)
        // get idx of first & last node of current level
        l, r := q[0].idx, q[size-1].idx
        // update if new max width
        if r - l + 1 > ans {
            ans = r - l + 1
        }
        // bfs
        for ; size > 0; size-- {
            // get pair
            currNode, currIdx := q[0].node, q[0].idx
            // pop
            q = q[1:]
            
            if currNode.Left != nil {
                q = append(q, Pair{currNode.Left, currIdx * 2})
            } 
            if currNode.Right != nil {
                q = append(q, Pair{currNode.Right, currIdx * 2 + 1})
            }
        }
    }
    return ans
}
```
### Java solution
```java
class Solution {
    class Pair {
        TreeNode node;
        int idx;
        Pair(TreeNode node, int idx) {
            this.node = node;
            this.idx = idx;
        }
    }
    public int widthOfBinaryTree(TreeNode root) {
        if(root == null)
            return 0;
        
        int ans = 0;
        Queue<Pair> q = new LinkedList();
        q.add(new Pair(root, 1));
        
        while(!q.isEmpty()) {
            int size = q.size(), l = 0, r = 0;
            for(int i = 0; i < size; i++) {
                int currIdx = q.peek().idx;
                TreeNode currNode = q.remove().node;
                
                if(currNode.left != null)
                    q.add(new Pair(currNode.left, currIdx * 2));
                if(currNode.right != null)
                    q.add(new Pair(currNode.right, currIdx * 2 + 1));
                
                if(i == 0)
                    l = currIdx;
                if(i == size - 1)
                    r = currIdx;
            }
            ans = Math.max(ans, r - l + 1);
        }
        return ans;
    }
}
```