# 269. Alien Dictionary (Hard)

There is a new alien language which uses the latin alphabet. However, the order among letters are
unknown to you. You receive a list of **non-empty words** from the dictionary, where words are 
**sorted lexicographically** by the rules of this new language. Derive the **order** of letters in
this language.

**Note**:
1. You may assume all letters are in lowercase.
2. If the order is invalid, return an empty string.
3. There may be multiple valid order of letters, return any one of them is fine.

- Example 1:
> **Input**: ["wrt", "wrf", "er", "ett", "rftt"] <br>
> **Output**: "wertf"
- Example 2:
> **Input**: ["z","x"] <br>
> **Output**: "zx"
- Example 3:
> **Input**: ["z","x","z"] <br>
> **Output**: ""

## Optimal
This problem expands [953-Verifying an Alien Dictionary](953-Verifying%20an%20Alien%20Dictionary.md)
since we're not given the order but we need to find the order.

We first need to stablish relations (**edges**) of chars (**nodes**) based on the words, and once we
have those relations, we can do a `topological sort`. 

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func alienOrder(words []string) string {
    ans := make([]rune, 0, 26)
    queue := make([]*Node, 0, 26)
    nodeMap := buildNodeMap(words)
    
    // invalid dictionary
    if nodeMap == nil {
        return ""
    }
    
    // get nodes with 0 indegree
    for _, node := range nodeMap {
        if node.indegree == 0 {
            queue = append(queue, node)
        }
    }
    
    // kahn's algorithm, add nodes with indegree 0 to ans
    // and reduce 
    for len(queue) > 0 {
        node := queue[0]
        queue = queue[1:] // remove first element
        
        ans = append(ans, node.val) // append node val to ans
        
        for k, _ := range node.children {
            child := nodeMap[k]
            child.indegree--
            if child.indegree == 0 {
                queue = append(queue, child)
            }
        }
    }
    
    // if ans doesn't have all the characters in it, there is a cycle
    if len(ans) == len(nodeMap) {
        return string(ans)
    }
    return ""
}

func buildNodeMap(words []string) map[rune]*Node {
    nodeMap := make(map[rune]*Node)
    // create the adjacency list for the chars in the alphabet
    for _, word := range words {
        for _, c := range word {
            if _, found := nodeMap[c]; !found {
                nodeMap[c] = NewNode(c)
            }
        }
    }
    
    // take two words and compare them, the first char that doesn't
    // match represents an edge between word1[j]->word2[j]
    for i := 1; i < len(words); i++ {
        word1, word2 := words[i-1], words[i]
        isPrefix := true
        for j := 0; j < len(word1) && j < len(word2); j++ {
            if word1[j] == word2[j] {
                continue
            }
            prevChar, currChar := rune(word1[j]), rune(word2[j]) 
            nodePrev, nodeCurr := nodeMap[prevChar], nodeMap[currChar]
            
            // if nodePrev doesn't have nodeCurr as children, add it
            if _, found := nodePrev.children[currChar]; !found {
                nodePrev.children[currChar] = true
                nodeCurr.indegree++
            }
            // if there was a mismatch there's no prefix
            isPrefix = false
            // need to break since we know nothing about the chars after the mismatch
            // e.g. "car" and "cel"... we know that "a" comes before "e", but we don't know
            // about "r" and "l"
            break 
        }
        // if word2 is prefix of word1, the input is invalid
        // i.e. word2 should come first... e.g. abc, ab
        if isPrefix && len(word1) > len(word2) {
            return nil
        }
    }
    return nodeMap
}

type Node struct {
    val rune
    children map[rune]bool
    indegree int
}

func NewNode(r rune) *Node {
    arr := make(map[rune]bool)
    node := Node{r, arr, 0}
    return &node
}
```
### Java solution
```java
class Solution {
    public String alienOrder(String[] words) {
        Map<Character,Node> map = buildAdjMap(words);
        if(map == null) // invalid order of words
            return "";
        
        Queue<Node> queue = new LinkedList();
        StringBuilder sb = new StringBuilder();
        
        // get nodes with 0-indegree
        for(Node node : map.values()) {
            if(node.indegree == 0) {
                queue.add(node);
            }
        }
        
        while(!queue.isEmpty()) {
            Node node = queue.remove();
            sb.append(node.val);
            // reduce indegree of childs of curr node
            for(char c : node.children) {
                Node child = map.get(c);
                child.indegree--;
                if(child.indegree == 0) {
                    queue.add(child);
                }
            }
        }
        
        // all the chars have been sorted
        if(sb.length() == map.size())
            return sb.toString();
        
        return "";
    }
    
    private Map<Character,Node> buildAdjMap(String[] words) {
        Map<Character,Node> map = new HashMap();
        // fill the map with the alphabet
        for(String word : words) {
            for(int i = 0; i < word.length(); i++) {
                char c = word.charAt(i);
                if(!map.containsKey(c))
                    map.put(c, new Node(c));
            }
        }
        // find relations between the chars
        for(int i = 1; i < words.length; i++) {
            String w1 = words[i-1], w2 = words[i];
            boolean isPrefix = true;
            // compare chars of both words
            for(int j = 0; j < w1.length() && j < w2.length(); j++) {
                if(w1.charAt(j) == w2.charAt(j))
                    continue;
                Node n1 = map.get(w1.charAt(j)), n2 = map.get(w2.charAt(j));
                // node1 might already have node2 as child, so we don't increase indegree
                if(!n1.children.contains(n2.val))
                {
                    n1.children.add(n2.val);
                    n2.indegree++;
                }
                
                isPrefix = false;
                break;
            }
            // if w2 is a prefix of w1, the order is invalid
            if(isPrefix && w1.length() > w2.length())
                return null;
        }
        
        return map;
    }
    
    class Node {
        char val;
        int indegree;
        Set<Character> children;
        
        Node(char val) {
            this.val = val;
            this.indegree = 0;
            this.children = new HashSet();
        }
    }
}
```