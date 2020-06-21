# 297. Serialize and Deserialize Binary Tree (Hard)

*Serialization* is the process of converting a data structure or object into a sequence of bits so
that it can be stored in a file or memory buffer, or transmitted across a network connection link to
 be reconstructed later in the same or another computer environment.

Design an algorithm to serialize and deserialize a binary tree. There is no restriction on how your
serialization/deserialization algorithm should work. You just need to ensure that a binary tree can
be serialized to a string and this string can be deserialized to the original tree structure.

- Example:

> You may serialize the following tree:

       1
      / \
      2 3
       / \
      4   5

> as "[1,2,3,null,null,4,5]"

## Optimal
We can use a `pre-order traversal`. For serializing we just need a *StringBuilder* and some
characters for *separating* values and for *null* values. 

Similarly, for deserializing we will be creating nodes based on the string values: first we split
the string by using the *separator*. But how do we know which is the next element to be processed?
We can use a linked list, so the next element will always be the head, and once a tree node has been
created we just remove the head of the list and process the next element.

![alt tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/Figures/297_BST.png)

**Time: O(N)** (N -> Size of the tree; number of nodes) <br> **Space: O(N)**

### Java solution
```java
public class Codec {
    private static final String NULL = "#";
    private static final String SEP = ",";
    
    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        serialize(root, sb);
        // Delete last comma (SEP)
        sb.deleteCharAt(sb.length() - 1);
        
        return sb.toString();
    }
    
    // Serialize using a Pre-Order traversal, appending to a StringBuilder
    private void serialize(TreeNode root, StringBuilder sb) {
        if(root == null) {
            sb.append(NULL);
            sb.append(SEP);
            return;
        }
        sb.append(root.val);
        sb.append(SEP);
        serialize(root.left, sb);
        serialize(root.right, sb);
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        // Split the input string by comma (SEP) and convert the
        // string to a LinkedList (don't use queue as it is less performant) 
        String[] nodes = data.split(SEP);
        LinkedList<String> tokens = new LinkedList(Arrays.asList(nodes));
        return deserialize(tokens);
    }
    
    // Deserialize using a Pre-Order travesal.
    // Poll next element from the linked-list
    private TreeNode deserialize(LinkedList<String> tokens) {
        if(tokens.isEmpty() || NULL.equals(tokens.getFirst())) {
            tokens.removeFirst();
            return null;
        }
        int val = Integer.parseInt(tokens.removeFirst());
        TreeNode root = new TreeNode(val);
        root.left = deserialize(tokens);
        root.right = deserialize(tokens);
        
        return root;
    }
}
```
