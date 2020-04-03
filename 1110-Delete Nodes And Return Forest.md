# 1110. Delete Nodes And Return Forest

Given the `root` of a binary tree (each node in the tree has a distinct value) and a list of nodes 
`to_delete`, return the roots of the trees in the remaining forest (a disjoint union of trees). 
You may return the result in any order.

- Example 1:
>           1    
>         /   \   
>        2     3  
>       / \   / \   
>      4   5 6   7 
> *to_delete* = [3,5]
> **Output**: [[1,2,null,4],[6],[7]]

## Brute Force
Nested loop: for each node traverse until a greater value is found

**Time: O(n²) <br> Space: O(1)**

## Optimal
Create a map/set with values *to_delete*. 
Make a `post-order traversal` keeping track of the **parent**, **result/forest** and 
**map/set to_delete**.<br>
When a node *has to be deleted*, check if children are not null (since it's a post-order traversal, 
if the children had to be deleted, then they should be null already), and add them to the result. 
<br>
Finally, set the parent left/right to null, (this node has to be deleted and is a child of a parent)

**Time: O(n)** (visiting all the nodes in post-order traversal) <br> 
**Space: O(n)** (keeping track of the map and memory stack)

### Go solution
```go
func delNodes(root *TreeNode, to_delete []int) []*TreeNode {
    var forest []*TreeNode
    toDeleteMap := make(map[int]interface{})
    for _, d := range to_delete {
        toDeleteMap[d] = nil
    }
    
    if _, found := toDeleteMap[root.Val]; !found {
        forest = append(forest, root)
    }
    
    postOrderTraversal(root, nil, &forest, &toDeleteMap)
    
    return forest
}

func postOrderTraversal(root, parent *TreeNode, forest *[]*TreeNode, 
                        toDeleteMap *map[int]interface{}) {
    if root == nil {
        return
    }
    postOrderTraversal(root.Left, root, forest, toDeleteMap)
    postOrderTraversal(root.Right, root, forest, toDeleteMap)
    
    if _, found := (*toDeleteMap)[root.Val]; found {
        if root.Left != nil {
            *forest = append(*forest, root.Left)
        }
        if root.Right != nil {
            *forest = append(*forest, root.Right)
        }
        
        if parent != nil {
            if root == parent.Left {
                parent.Left = nil
            }
            if root == parent.Right {
                parent.Right = nil
            }
        }
    }
}
```
### Java solution
```java
class Solution {
    public List<TreeNode> delNodes(TreeNode root, int[] to_delete) {
        List<TreeNode> forest = new ArrayList();
        Set<Integer> nodesToDelete = new HashSet();
        for(int i : to_delete) {
            nodesToDelete.add(i);
        }
        
        if(!nodesToDelete.contains(root.val)){
            forest.add(root);
        }
        
        preOrder(root, null, nodesToDelete, forest);
        
        return forest;
    }
    
    private void preOrder(TreeNode root, TreeNode parent, Set<Integer> nodesToDelete,
                         List<TreeNode> forest) {
        if(root == null)
            return;
        
        preOrder(root.left, root, nodesToDelete, forest);
        preOrder(root.right, root, nodesToDelete, forest);
        
        if(nodesToDelete.contains(root.val)) {
            if(root.left != null)
                forest.add(root.left);
            
            if(root.right != null)
                forest.add(root.right);
            
            if(parent != null) {
                if(root == parent.left) {
                    parent.left = null;
                }
                if(root == parent.right) {
                    parent.right = null;
                }
            }
        }
    }
}
```