# 809. Expressive Words (Medium)

Sometimes people repeat letters to represent extra feeling, such as "hello" -> "heeellooo", 
"hi" -> "hiiii".  In these strings like "heeellooo", we have groups of adjacent letters that are all
the same:  "h", "eee", "ll", "ooo".

For some given string `S`, a query `word` is **stretchy** if it can be made to be equal to `S` by
any number of applications of the following extension operation: choose a group consisting of
characters `c`, and add some number of characters `c` to the group so that the size of the group is 
*3 or more*.

For example, starting with "hello", we could do an extension on the group "o" to get "hellooo", but
we cannot get "helloo" since the group "oo" has size less than 3.  Also, we could do another
extension like "ll" -> "lllll" to get "helllllooo".  If S = "helllllooo", then the query word 
"hello" would be stretchy because of these two extension operations: query = "hello" -> "hellooo" ->
"helllllooo" = S.

Given a list of query words, return the number of words that are stretchy. 

- Example 1:
> **Input**: S = "heeellooo", words = ["hello", "hi", "helo"] <br>
> **Output**: 1 <br>
> **Explanation**: We can extend "e" and "o" in the word "hello" to get "heeellooo". <br>
> We can't extend "helo" to get "heeellooo" because the group "ll" is not size 3 or more.

## Optimal
First we need to *compress* `S` and then compress each *word* to validate it is stretchy. But how do
we *compress* a string? We just return the *characters* and their *counts*; for example, for 
*S="heeello"*, we have *['h','e','l','o'] and [1,3,2,1]*.

Then, how do we know if a *word* is stretchy?
- If a *word* is larger than `S`, the it is not stretchy.
- If a *compressed* word has different chars as *Compressed S*, then it is not stretchy.
- If every char in *compressed* word has the *same* count for all the chars in `S`, the word is
  stretchy.
- Alternatively, if 0 or more chars don't match counts but the count in `S` *>= 3*, then it is
  stretchy. E.g. *S="heeello"* and *w='hello'* we have counts *[1,3,2,1] and [1,1,2,1]*.

**Time: O(W\*Q)** (W -> number of words, Q -> length of the max string) <br> **Space: O(Q)**

### Go solution
```go
func expressiveWords(S string, words []string) int {
    // get compressed arrays of S
    charsS, countsS := compress(S)
    ans := 0
    // check if each word is stretchy
    for _, w := range words {
        // word is larger than S, so it can't be stretchy
        if len(w) > len(S) {
            continue
        }
        charsW, countsW := compress(w)
        if isStretchy(charsS, charsW, countsS, countsW) {
            ans++
        }
    }
    return ans
}

// get a rune/int array for the chars their occurrences in a given string
// e.g. "heeello" -> [h, e, l, o], [1, 3, 2, 1]
func compress(str string) ([]rune, []int) {
    n := len(str)
    chars := make([]rune, 0, n)
    counts := make([]int, 0, n)
    for _, c := range str {
        if len(chars) == 0 || chars[len(chars) - 1] != c {
            chars = append(chars, c)
            counts = append(counts, 1)
        } else {
            counts[len(counts) - 1]++
        }
    }
    return chars, counts
}

func isStretchy(charsS, charsW []rune, countsS, countsW []int) bool {
    // different char lengths
    if len(charsS) != len(charsW) {
        return false
    }
    // to be stretchy:
    // 1. all chars have to match and 
    // 2. char counts should be equal or count in S should be >=3, so char in W can stretch
    matches := 0
    for i := 0; i < len(charsS); i++ {
        if charsS[i] == charsW[i] && (countsS[i] == countsW[i] || countsS[i] >= 3) {
            matches++
        }
    }
    // all chars/counts match
    if matches == len(charsS) {
        return true
    }
    return false
}
```
