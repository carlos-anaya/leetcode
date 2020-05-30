# 968. Binary Tree Cameras (Hard)

Given a *binary tree*, we install *cameras* on the nodes of the tree. 

Each camera at a node can **monitor its parent, itself, and its immediate children**.

Calculate the *minimum number of cameras* needed to monitor all nodes of the tree.

- Example 1:
> **Input**: [0,0,null,0,0] <br>
> **Output**: 1 <br>
> **Explanation**: One camera is enough to monitor all nodes if placed as shown below.

![tree1](https://assets.leetcode.com/uploads/2018/12/29/bst_cameras_01.png)

- Example 2:
> **Input**: [0,0,null,0,null,0,null,null,0] <br>
> **Output**: 2 <br>
> **Explanation**: At least two cameras are needed to monitor all nodes of the tree. The image below
> shows one of the valid configurations of camera placement.

![tree2](https://assets.leetcode.com/uploads/2018/12/29/bst_cameras_02.png)

## Optimal
Let's analize the problem and some properties of the nodes when placing cameras on them or around:
- A **leaf** node can be "monitored" by itself or its parent. (2 options)
- A **root** node can be "monitored" by itself or any of its children. (3 options)
- An **"inner"** node can be "monitored" by itself, its parent or any of its children. (4 options)

Then, if we set a camera in a leave it will only cover two nodes at most, so we instead put a camera
in the leaves' parents, which will monitor more nodes. Once we set a camera in a node, it's parent
becomes a "leaf" (already monitored), so we go to it's grandparent and so on.

For the solution we do a `post-order` traversal and `greedily` place cameras, so for the current
node we process the children first and then we decide what to do with the current node.
- If **both** children are *MONITORED* then this node is *UNMONITORED*. Leave nodes return
  *UNMONITORED* as well. For simplicity, null nodes (leaves children) are considered *MONITORED*.
- If **any** of the children is *UNMONITORED* then we have to place a *CAMERA* in this node and
  increase the *camera count*.
- Any other scenario will involve a *CAMERA* child, so the current node is *MONITORED*.

**Time: O(n) <br> Space: O(1)**

### Go solution
```go
const (
    UNMONITORED = 0
    MONITORED = 1
    CAMERA = 2
)

var minCam int

func minCameraCover(root *TreeNode) int {
    minCam = 0
    // postorder traversal
    // if root was left unmonitored we need one camera
    if postorder(root) == UNMONITORED {
        minCam++
    }
    
    return minCam
}

func postorder(root *TreeNode) int {
    if root == nil {
        return MONITORED
    }
    l := postorder(root.Left)
    r := postorder(root.Right)
    
    // node is leave or children are monitored
    // can be left unmonitored and parent will monitor it
    if l == MONITORED && r == MONITORED {
        return UNMONITORED
    }
    // at least one child is unmonitored, so we place a camera here
    if l == UNMONITORED || r == UNMONITORED {
        minCam++
        return CAMERA
    }
    // at least one child has a camera so this node is monitored
    return MONITORED
}
```
### Java solution
```java
class Solution {
    enum status { UNMONITORED, MONITORED, CAMERA }
    int camCount;
    
    public int minCameraCover(TreeNode root) {
        camCount = 0;
        // do a preorder traversal
        // if root was left unmonitored we need one camera for it
        if(preorder(root) == status.UNMONITORED)
            camCount++;
        return camCount;
    }
    
    private status preorder(TreeNode root) {
        if(root == null)
            return status.MONITORED;
        
        status l = preorder(root.left);
        status r = preorder(root.right);
        
        // node is leaf or both children are monitored, so this can be
        // unmonitored and the parent can monitor it
        if(l == status.MONITORED && r == status.MONITORED)
            return status.UNMONITORED;
        // any of the children is unmonitored, so we need to put a camera
        if(l == status.UNMONITORED || r == status.UNMONITORED) {
            camCount++;
            return status.CAMERA;
        }
        // at this point there's at least one camera
        return status.MONITORED;
    }
}
```