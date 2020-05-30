# 173. Binary Search Tree Iterator (Medium)

Implement an `iterator` over a *binary search tree (BST)*. Your iterator will be initialized with
the root node of a BST.

**Notes**:
- Calling `next()` will return the next smallest number in the BST.
- `next()` and `hasNext()` should run in **average O(1) time** and use **O(h) memory**, where *h*
  is the height of the tree.
- You may assume that `next()` call will always be valid, that is, there will be at least a next
  smallest number in the BST when next() is called.

- Example 1:
> BSTIterator iterator = new BSTIterator(root); <br>
> iterator.next();    // return 3 <br>
> iterator.next();    // return 7 <br>
> iterator.hasNext(); // return true <br>
> iterator.next();    // return 9 <br>
> iterator.hasNext(); // return true <br>
> iterator.next();    // return 15 <br>
> iterator.hasNext(); // return true <br>
> iterator.next();    // return 20 <br>
> iterator.hasNext(); // return false

![tree](https://assets.leetcode.com/uploads/2018/12/25/bst-tree.png)

## Brute Force
Create a `LinkedList/Queue/Array` when the BSTIterator is instantiated. Then just return next until
list/queue is empty or the idx var reached the end of the array.

**Time: Initialize O(n), hasNext/next O(1) <br> Space: O(n)**

## Optimal
One of the requirements is to use **O(h)** space, so we can't store every node. Then, we use a
`stack` and keep pushing elements to the left. Then, everytime `next()` is called, the *stack.peek*
gets popped, and we push to the stack the *right* node and it's left children. 

**Time: Initialize O(n), hasNext/next O(1)** (amortized) <br> **Space: O(h)**

### Go solution
```go
type BSTIterator struct {
    stack []*TreeNode
}

func Constructor(root *TreeNode) BSTIterator {
    stack := make([]*TreeNode, 0, 100)
    iterator := BSTIterator{stack}
    iterator.dfs(root)
    
    return iterator
}

/** @return the next smallest number */
func (this *BSTIterator) Next() int {
    // get peek node
    node := this.stack[len(this.stack) - 1]
    // pop the peek element
    this.stack = this.stack[:len(this.stack) - 1]
    // add right child and left grandchilds if any
    this.dfs(node.Right)
    
    return node.Val
}

/** @return whether we have a next smallest number */
func (this *BSTIterator) HasNext() bool {
    return len(this.stack) > 0
}

func (this *BSTIterator) dfs(root *TreeNode) {
    for root != nil {
        this.stack = append(this.stack, root)
        root = root.Left
    }
}
```
### Java solution
```java
class BSTIterator {
    Stack<TreeNode> stack;

    public BSTIterator(TreeNode root) {
        this.stack = new Stack();
        dfs(root);
    }
    
    /** @return the next smallest number */
    public int next() {
        TreeNode node = this.stack.pop();
        if(node.right != null) {
            dfs(node.right);
        }
        
        return node.val;
    }
    
    /** @return whether we have a next smallest number */
    public boolean hasNext() {
        return this.stack.size() > 0;
    }
    
    private void dfs(TreeNode root) {
        while(root != null) {
            this.stack.push(root);
            root = root.left;
        }
    }
}
```