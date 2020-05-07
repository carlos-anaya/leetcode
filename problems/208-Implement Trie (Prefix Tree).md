# 208. Implement Trie (Prefix Tree)

Implement a trie with `insert`, `search`, and `startsWith` methods.

- Example 1:
> Trie trie = new Trie(); <br>
> trie.insert("apple"); <br>
> trie.search("apple");   // returns true <br>
> trie.search("app");     // returns false <br>
> trie.startsWith("app"); // returns true <br>
> trie.insert("app"); <br>
> trie.search("app");     // returns true

## Optimal
Define a `TrieNode` struct/class which will hold a *character*, a mark to check if it's a full word
and a list of `children`. We can use an array of chars or a map. We'll go for a map since an array
will be more memory intensive. The `Trie` structure just needs a **root** TrieNode.

- When adding a word, loop through the chars and check whether there's a child or not. Add the nodes
needed and at the end mark the last node as *isWord*.
- When searching for a word/prefix, we can use the same search logic and just use a boolean to check
whether we want a prefix or a full word. When a child is not found return *false*

**INSERT<br> Time: O(m) <br> Space: O(m)**<br>
**SEARCH<br> Time: O(m) <br> Space: O(1)**<br>
*m -> size of the word/number of nodes*

### Go solution
```go
type Trie struct {
    root *TrieNode
}

/** Initialize your data structure here. */
func Constructor() Trie {
    root := NewTrieNode('/')
    return Trie{&root}
}


/** Inserts a word into the trie. */
func (this *Trie) Insert(word string)  {
    curr := this.root
	for _, char := range word {
		if child, found := curr.GetChild(char); found {
			curr = child
		} else {
			newNode := NewTrieNode(char)
			curr.AddChild(&newNode)
			curr = &newNode
		}
	}
	curr.SetIsWord(true)
}


/** Returns if the word is in the trie. */
func (this *Trie) Search(word string) bool {
    return search(this.root, word, true)
}


/** Returns if there is any word in the trie that starts with the given prefix. */
func (this *Trie) StartsWith(prefix string) bool {
    return search(this.root, prefix, false)
}

func search(root *TrieNode, word string, fullWord bool) bool {
	for _, char := range word {
		if child, found := root.GetChild(char); found {
			root = child
		} else {
			return false
		}
	}

	return !fullWord || root.IsWord()
}

type TrieNode struct {
	char     rune
	isWord   bool
	children map[rune]*TrieNode
}

func NewTrieNode(char rune) TrieNode {
	children := make(map[rune]*TrieNode)
	return TrieNode{char, false, children}
}

func (n *TrieNode) AddChild(child *TrieNode) {
	n.children[child.char] = child
}

func (n *TrieNode) GetChild(char rune) (*TrieNode, bool) {
	child, found := n.children[char]
	return child, found
}

func (n *TrieNode) IsWord() bool {
	return n.isWord
}

func (n *TrieNode) SetIsWord(val bool) {
	n.isWord = val
}
```
### Java solution
```java
class Trie {
    
    TrieNode root;

    /** Initialize your data structure here. */
    public Trie() {
        root = new TrieNode('/');
    }
    
    /** Inserts a word into the trie. */
    public void insert(String word) {
        TrieNode curr = this.root;
        for(int i = 0; i < word.length(); i++) {
            char c = word.charAt(i);
            TrieNode child = curr.GetChild(c);
            if(child == null) {
                child = new TrieNode(c);
                curr.addChild(child);
            }
            curr = child;
        }
        curr.SetIsWord(true);
    }
    
    /** Returns if the word is in the trie. */
    public boolean search(String word) {
        return search(this.root, word, false);
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    public boolean startsWith(String prefix) {
        return search(this.root, prefix, true);
    }
    
    private boolean search(TrieNode root, String word, boolean isPrefix) {
        for(int i = 0; i < word.length(); i++) {
            char c = word.charAt(i);
            TrieNode child = root.GetChild(c);
            if(child != null) {
                root = child;
            } else {
                return false;
            }
        }

        return isPrefix || root.IsWord();
    }
    
    class TrieNode {
        char val;
        boolean isWord;
        Map<Character, TrieNode> children;
        
        
        public TrieNode(char val) {
            this.children = new HashMap();
            this.val = val;
        }
        
        public void addChild(TrieNode child) {
            this.children.put(child.val, child);
        }

        public TrieNode GetChild(char val) {
            return this.children.get(val);
        }
        
        public boolean IsWord() {
            return this.isWord;
        }

        public void SetIsWord(boolean val) {
            this.isWord = val;
        }
    }
}
```