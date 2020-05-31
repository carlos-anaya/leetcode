# 212. Word Search II (Hard)

Given a `2D board` and a list of `words` from the dictionary, find **all words** in the board.

Each word must be constructed from letters of sequentially **adjacent** cell, where "adjacent" cells
are those horizontally or vertically neighboring. The same letter cell may not be used more than
once in a word.

- Example 1:
> **Input**: words = ["oath","pea","eat","rain"], board = [ <br>
> [**o**,**a**,a,n], <br>
> [e,**t**,**a**,**e**], <br>
> [i,**h**,k,r], <br>
> [w,f,r,a] <br>
> ]
> **Output**: ["eat","oath"] <br>
> **Explanation**: The board has the found words in **bold**

## Brute Force
We follow the same approach as in [79-Word Search](79-Word%20Search.md). We use a `backtracking`
approach to check if a word exists in the board and repeat the same for every word.

**Time: O(W\*M\*N\*4^L)** (L -> lenght of word; 4^L because we check 4 paths of at most L chars,
M, N -> dimensions of the board, W -> number of words) <br>
**Space: O(L)**

## Optimal
The approach above is very repetitive, for each word we traverse the whole board over and over
again. Instead, we use a `Trie` to store the words, so words that share a prefix will be found in
the same backtracking process. I.e. "dog" and "dogs" will be found in the same path. This way we
only do one traversal over the whole board.

Then, the algorithm is as follows:
- Build the trie given the words.
- Loop over the cells of the board. For each cell check if there's a node in the trie that matches
  the character.
- When there's a match, backtrack over the neighbors. If the current child is a "word", we add it to
  the answer list.

To optimize, we prune the leaves that match a word once they has been processed and they have no
children. Addidionally, we store the word directly in the nodes that mark the end of a word.

![trie](https://leetcode.com/problems/word-search-ii/Figures/212/212_trie_algo.png)

**Time: O(M\*N\*4^L)** (L -> max lenght of words; 4^L because we check 4 paths of at most L chars)
<br> **Space: O(D)** (D -> number of chars in the dictionary)

### Go solution
```go
// use a single object to keep track of input args and keep backtraking function small
type Metadata struct {
    board [][]byte
    ans []string
    rows int
    cols int
}

func findWords(board [][]byte, words []string) []string {
    ans := make([]string, 0, len(words))
    metadata := Metadata{ board, ans, len(board), len(board[0]) }
    trie := buildTrie(words)
    
    // loop over the board once and for each cell check if there's a node in the trie that matches
    for i := 0; i < metadata.rows; i++ {
        for j := 0; j < metadata.cols; j++ {
            checkBoard(&metadata, i, j, trie)
        }
    }
    
    return metadata.ans
}

func checkBoard(metadata *Metadata, i, j int, trie *TrieNode) {
    // invalid board position or already used char
    if i < 0 || i >= metadata.rows || j < 0 || j >= metadata.cols ||
        metadata.board[i][j] == byte('.') {
        return
    }
    currChar := metadata.board[i][j]
    
    // trie don't have a child that matches this cell
    child := trie.children[currChar]
    if child == nil {
        return
    }
    
    // found a full word match, append it to results
    if child.word != "" {
        metadata.ans = append(metadata.ans, child.word)
        // avoid counting the same word again
        child.word = ""
        // prune the child if it is a leave
        if len(child.children) == 0 {
            trie.removeChild(child.val)
            return // no need to backtrack since there are no children
        }
    }
    
    // backtrack in neighbors
    metadata.board[i][j] = byte('.')

    checkBoard(metadata, i+1, j, child)
    checkBoard(metadata, i-1, j, child)
    checkBoard(metadata, i, j-1, child)
    checkBoard(metadata, i, j+1, child)

    metadata.board[i][j] = currChar
}

func buildTrie(words []string) *TrieNode {
    root := NewTrieNode('/')
    
    for _, word := range words {
        curr := root
        for _, c := range word {
            curr = curr.addChild(byte(c))
        }
        curr.word = word
    }
    
    return root
}

type TrieNode struct {
    val byte
    children map[byte]*TrieNode
    word string
}

func NewTrieNode(val byte) *TrieNode {
    node := TrieNode{val, make(map[byte]*TrieNode), ""}
    return &node
}

func (n *TrieNode) addChild(val byte) *TrieNode {
    if child, found := n.children[val]; found {
        return child
    }
    child := NewTrieNode(val)
    n.children[val] = child
    return child
}

func (n *TrieNode) removeChild(val byte) {
    delete(n.children, val)
}
```
### Java solution
```java
class Solution {
    Metadata metadata;
    
    public List<String> findWords(char[][] board, String[] words) {
        metadata = new Metadata(board);
        TrieNode trie = buildTrie(words);
        
        for(int i = 0; i < metadata.rows; i++) {
            for(int j = 0; j < metadata.cols; j++) {
                // if(trie.children.containsKey(board[i][j]))
                    backtrack(trie, i, j);
            }
        }
        
        return metadata.ans;
    }
    
    private void backtrack(TrieNode trie, int i, int j) {
        // invalid board position
        if(i < 0 || i >= metadata.rows || j < 0 || j >= metadata.cols
            || metadata.board[i][j] == '.')
            return;
        
        char curr = metadata.board[i][j];
        TrieNode child = trie.children.get(curr);
        if(child == null) // no match of trie node and cell value
            return;

        // child is a word
        if(child.word != null) {
            metadata.ans.add(child.word);
            // remove reference to word so node doesn't count anymore
            child.word = null;
            // prune child if it is a leave
            if(child.children.size() == 0) {
                trie.children.remove(child.val);
                return; // child was a leave, so no need to keep backtracking
            }
        }
        
        // backtrak over its neighbors
        metadata.board[i][j] = '.';

        backtrack(child, i-1, j);
        backtrack(child, i+1, j);
        backtrack(child, i, j-1);
        backtrack(child, i, j+1);

        metadata.board[i][j] = curr;
    }
    
    private TrieNode buildTrie(String[] words) {
        TrieNode root = new TrieNode('/');
        for(String w : words) {
            TrieNode curr = root;
            for(int i = 0; i < w.length(); i++) {
                curr = curr.addChild(w.charAt(i));
            }
            curr.word = w;
        }
        
        return root;
    }
    
    class Metadata {
        char[][] board;
        List<String> ans;
        int rows;
        int cols;
        
        Metadata(char[][] board) {
            this.board = board;
            this.ans = new ArrayList();
            this.rows = board.length;
            this.cols = board[0].length;
        }
    }
    
    class TrieNode {
        char val;
        Map<Character,TrieNode> children;
        String word;
        
        TrieNode(char val) {
            this.val = val;
            this.children = new HashMap();
        }
        
        TrieNode addChild(char val) {
            TrieNode child = this.children.get(val);
            if(child != null)
                return child;
            
            child = new TrieNode(val);
            this.children.put(val, child);
            return child;
        }
    }
}
```