# 211. Add and Search Word - Data structure design

Design a data structure that supports the following two operations:

- void addWord(word)
- bool search(word)

*search(word)* can search a literal word or a *regular expression* string containing only 
letters **a-z** or **'.'**. A **'.'** means it can represent any one letter.

- Example:
> addWord("bad") <br>
> addWord("dad") <br>
> addWord("mad") <br>
> search("pad") -> false <br>
> search("bad") -> true <br>
> search(".ad") -> true <br>
> search("b..") -> true

## Optimal
This is a variation of [208-Implement Trie (Prefix Tree)](208-Implement%20Trie%20(Prefix%20Tree).md)
Use a `trie` data structure. The *addWord* method is the same as in a regular trie. The *searchWord*
method is a bit different since we need to support the `.` special char.
- When searching for a word, evaluate char by char.
- If *char* is a *character* and *curr node* doesn't have that child, return **false**
- If *char* is a *character* and *curr node* has that child, set *curr node = child[char]*
- If *char* is a **'.'**, then we have to *search recursively* on each of it's children.
- At the end, return whether the last node *isWord*.

**INSERT<br> Time: O(k) <br> Space: O(k)**<br>
**SEARCH<br> Time: O(k\*m) <br> Space: O(k\*m)**<br>
*k -> average of word's length, m -> number of words*

### Go solution
```go
type WordDictionary struct {
    root *TrieNode
}

/** Initialize your data structure here. */
func Constructor() WordDictionary {
    root := NewTrieNode('/')
    dict := WordDictionary{&root}
    
    return dict
}

/** Adds a word into the data structure. */
func (this *WordDictionary) AddWord(word string)  {
    curr := this.root
    for _, char := range word {
        if _, found := curr.children[char]; !found {
            child := NewTrieNode(char)
            curr.children[char] = &child
        }
        
        curr = curr.children[char]
    }
    curr.isWord = true
}

/** Returns if the word is in the data structure. A word could contain the dot character '.' to represent any one letter. */
func (this *WordDictionary) Search(word string) bool {
    curr := this.root
    return search(curr, word)
}

func search(root *TrieNode, word string) bool {
    for i, char := range word {
        if char == '.' {
            // in case of a wildcard, we need to check every child for the remaining string
            for _, child := range root.children {    
                if search(child, word[i+1:]) {
                    return true
                }
            }
            return false
        } 
        // curr char is a character, so we check if it a child matches
        if child, found := root.children[char]; !found {
            return false
        } else {
            root = child
        }
    }
    return root.isWord
}

type TrieNode struct {
    val rune
    children map[rune]*TrieNode
    isWord bool
}

func NewTrieNode(val rune) TrieNode {
    children := make(map[rune]*TrieNode)
    return TrieNode{val, children, false} 
}

func (t *TrieNode) addChild(val rune) *TrieNode {
    if child, found := t.children[val]; found {
        return child
    }
    child := NewTrieNode(val)
    t.children[val] = &child
    
    return &child
}
```
### Java solution
```java
class WordDictionary {
    class TrieNode {
        char c;
        Map<Character, TrieNode> children;
        boolean isWord;
        
        TrieNode(char c) {
            this.c = c;
            this.children = new HashMap();
        }
    }

    TrieNode root;
    
    /** Initialize your data structure here. */
    public WordDictionary() {
        root = new TrieNode('/');
    }
    
    /** Adds a word into the data structure. */
    public void addWord(String word) {
        TrieNode curr = this.root;
        for(int i = 0; i < word.length(); i++) {
            char c = word.charAt(i);
            if(!curr.children.containsKey(c)) {
                curr.children.put(c, new TrieNode(c));
            }
            curr = curr.children.get(c);
        }
        curr.isWord = true;
    }
    
    /** Returns if the word is in the data structure. A word could contain the dot character '.' to represent any one letter. */
    public boolean search(String word) {
        return search(this.root, word, 0);
    }
    
    private boolean search(TrieNode root, String word, int idx) {
        if(idx >= word.length()) {
            return root.isWord;
        }
        
        for(int i = idx; i < word.length(); i++) {
            char c = word.charAt(i);
            
            if(c == '.') {
                for(char child : root.children.keySet()) {
                    if(search(root.children.get(child), word, i + 1)) {
                        return true;
                    }
                }
                return false;
            }
            
            root = root.children.get(c);
            if(root == null) {
                return false;
            }
        }
        
        return root.isWord;
    }
}
```