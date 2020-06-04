# 127. Word Ladder (Medium)

Given two words (`beginWord` and `endWord`), and a dictionary's `wordList`, find the length of the
*shortest* transformation sequence from `beginWord` to `endWord`, such that:

1. Only one letter can be changed at a time.
2. Each transformed word must exist in the word list.

**Note**:

- Return 0 if there is no such transformation sequence.
- All words have the same length.
- All words contain only lowercase alphabetic characters.
- You may assume no duplicates in the word list.
- You may assume beginWord and endWord are non-empty and are not the same.

- Example 1:
> **Input**: beginWord="hit", endWord="cog", wordList=["hot","dot","dog","lot","log","cog"] <br>
> **Output**: 5 <br>
> **Explanation**: the shortest transformation is "hit" -> "hot" -> "dot" -> "dog" -> "cog"
- Example 2:
> **Input**: beginWord="hit" endWord="cog" wordList=["hot","dot","dog","lot","log"] <br>
> **Output**: 0 <br>
> **Explanation**: The endWord "cog" is not in wordList, therefore no possible transformation.

## Optimal - I
This is a graph problem and since it asks for the shortest transformation sequence we can use a
`bread-first search`. We can see the *words* as **vertices** and the **edges** represent
associations between words that are 1-edit distance appart, e.g. *bit*, *bat*, *bet*.

Now, for a given word we find the **adjacent nodes** by generating the words that are 1-edit
distance apart for each of the positions of the word, and check if they're in the initial
**word list** dictionary.

![href words graph](https://leetcode.com/problems/word-ladder/Figures/127/Word_Ladder_1.png)

**Time: O(W\*K²)** (W -> number of words, K -> length of the word) <br> **Space: O(W\*K²)**

## Optimal - II
We can do a `Bidirectional Breadth First Search`, reducing the number of words explored. We find
form *startWord* and from *endWord* and if they meet at any time, we have a **match**.

### Go solution
```go
// one-directional (regular) BFS
func ladderLength(beginWord string, endWord string, wordList []string) int {
    w, k := len(wordList), len(beginWord)
    dict := make(map[string]bool)
    for _, word := range wordList {
        dict[word] = true
    }
    // short-circuit if endWord is not in dict
    if _, found := dict[endWord]; !found {
        return 0;
    }
    q := make([]string, 0, w)
    q = append(q, beginWord)
    movs := 1
    
    for {
        size := len(q)
        if size == 0 { break }
        // pop all the words of the level
        for ; size > 0; size-- {
            curr := q[0]
            q = q[1:] // poll from queue
            for i := 0; i < k; i++ {
                for c := 'a'; c <= 'z'; c++ {
                    tempWord := curr[:i] + string(c) + curr[i+1:]
                    if _, found := dict[tempWord]; found {
                        if tempWord == endWord {
                            return movs + 1
                        }
                        // append neighbor from the queue
                        q = append(q, tempWord)
                        // remove neighbor from dict, so we don't reuse it
                        delete(dict, tempWord)
                    }
                }
            }
        }
        movs++
    }
    // no solution
    return 0
}
```
### Java solution
```java
class Solution {
    // one-directional (regular) BFS
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        Set<String> dict = new HashSet(wordList);
        // short-circuit if "endWord" is not in the wordList
        if(!dict.contains(endWord))
            return 0;
        
        dict.remove(beginWord); // remove words used from the dict so we don't reuse them
        Queue<String> queue = new LinkedList();
        queue.add(beginWord);
        int movs = 1;
        
        while(!queue.isEmpty()) {
            // get the number of nodes (words) of the level
            int size = queue.size();
            // poll the nodes of the level
            for(int s =0; s < size; s++) {
                String curr = queue.poll();
                // loop over each of the chars of word
                for(int i = 0; i < curr.length(); i++) {
                    char[] chars = curr.toCharArray();
                    // loop over the alphabet, replace char and check if
                    // the word exists in the dictionary
                    for(char c = 'a'; c <= 'z'; c++) {
                        chars[i] = c;
                        String temp = new String(chars);
                        
                        // word not in the dictionary
                        if(!dict.contains(temp))
                            continue;

                        // found a solution.
                        if(temp.equals(endWord))
                            return movs + 1;

                        // if not already visited, add the word to the queue
                        queue.add(temp);
                        // remvove used word from the dict
                        dict.remove(temp);
                    }
                }
            }
            movs++;
        }
        // no answer
        return 0;
    }
}
```