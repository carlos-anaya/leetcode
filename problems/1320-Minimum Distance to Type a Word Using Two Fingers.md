# 1320. Minimum Distance to Type a Word Using Two Fingers (Hard)

You have a keyboard layout as shown above in the XY plane, where each English uppercase letter is
located at some coordinate, for example, the letter **A** is located at coordinate **(0,0)**, the
letter **B** is located at coordinate **(0,1)**, the letter **P** is located at coordinate **(2,3)**
and the letter **Z** is located at coordinate **(4,1)**.

Given the string word, return the *minimum* total distance to type such string using only two 
fingers. The distance between coordinates **(x1,y1)** and **(x2,y2)** is **|x1 - x2| + |y1 - y2|**. 

Note that the initial positions of your two fingers are considered free so don't count towards your
total distance, also your two fingers do not have to start at the first letter or the first two
letters.

![alt keyboard](https://assets.leetcode.com/uploads/2020/01/02/leetcode_keyboard.png)

- Example 1:
> **Input**: word = "CAKE" <br>
> **Output**: 3 <br>
> **Explanation**: Using two fingers, one optimal way to type "CAKE" is:  <br>
> Finger 1 on letter 'C' -> cost = 0  <br>
> Finger 1 on letter 'A' -> cost = Distance from letter 'C' to letter 'A' = 2  <br>
> Finger 2 on letter 'K' -> cost = 0  <br>
> Finger 2 on letter 'E' -> cost = Distance from letter 'K' to letter 'E' = 1  <br>
> Total distance = 3
- Example 2:
> **Input**: word = "HAPPY" <br>
> **Output**: 6 <br>
> **Explanation**:
> Using two fingers, one optimal way to type "HAPPY" is: <br>
> Finger 1 on letter 'H' -> cost = 0 <br>
> Finger 1 on letter 'A' -> cost = Distance from letter 'H' to letter 'A' = 2 <br>
> Finger 2 on letter 'P' -> cost = 0 <br>
> Finger 2 on letter 'P' -> cost = Distance from letter 'P' to letter 'P' = 0 <br>
> Finger 1 on letter 'Y' -> cost = Distance from letter 'A' to letter 'Y' = 4 <br>
> Total distance = 6
- Example 3:
> **Input**: word = "YEAR" <br>
> **Output**: 7

## Brute Force - Recursion
Plain recursion, check both paths (using each finger) and return the *min* value. We can keep the
letters in a map, so we can access the coordinate in the keyboard. Distance is calculated based on
the finger position and the letter position. Start with an empty array, so the first movement is
ignored (i.e. cost is 0).

**Time: O(2^n) <br> Space: O(n)**

```go
var keyboard map[byte][]int

func minimumDistance(word string) int {
    keyboard = initKeyboard()
    
    return rec(word, 0, []int{}, []int{})
}

func rec(word string, w int, pos1, pos2 []int) int {
    // base case
    if w >= len(word) {
        return 0
    }
    
    keyPos := keyboard[word[w]]
    // explore two paths, one for each finger
    // calculate the distance to move finger to letter
    // explore the path for next char in word, finger stays in last position
    path1 := dist(pos1, keyPos) + 
        rec(word, w+1, keyPos, pos2)
    path2 := dist(pos2, keyPos) + 
        rec(word, w+1, pos1, keyPos)
    
    return min(path1, path2)
}

func dist(curr, dest []int) int {
    // finger don't have a previous position
    if len(curr) == 0 {
        return 0
    }
    // get distance from finger position to key
    return abs(curr[0] - dest[0]) + abs(curr[1] - dest[1])
}

func initKeyboard() map[byte][]int{
    m := make(map[byte][]int)
    for i := 0; i <= 26; i++ {
        m[byte('A' + i)] = []int{i / 6, i % 6}
    } 
    return m
}
```

## Optimal I - Memoization
This problem has *overlapping subproblem* property, so we can use a `memo array` to avoid
*recalculating* subproblems. 
- We don't need the **keyboard map** to store the letter's position in the keyboard, we can
  calculate the *<x,y>* coordinates on the fly.
- The *memo* array should have size *27, 27, len(word)*. But why 27? Because we have 26 positions 
  for the characters (alphabet A:0 to Z:25) plus the initial "free" position. We use 26 to identify
  that initial position so the cost for it is 0.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
var memo [27][27][]int

func minimumDistance(word string) int {
    // initialize memo
    memo = [27][27][]int{}
    
    return rec(word, 0, 26, 26)
}

func rec(word string, w int, pos1, pos2 int) int {
    // base case
    if w >= len(word) {
        return 0
    }
    
    // initialize memo in pos1, pos2 if not existing
    if len(memo[pos1][pos2]) == 0 {
        memo[pos1][pos2] = make([]int, len(word))
    }
    // path already visited
    if memo[pos1][pos2][w] != 0 {
        return memo[pos1][pos2][w]
    }
    
    // explore two paths, one for each finger
    keyPos := int(word[w] - 'A')
    path1 := dist(pos1, keyPos) + rec(word, w+1, keyPos, pos2)
    path2 := dist(pos2, keyPos) + rec(word, w+1, pos1, keyPos)
    
    // update memo
    memo[pos1][pos2][w] = min(path1, path2) 
    return memo[pos1][pos2][w]
}

func dist(curr, dest int) int {
    // finger don't have a previous position
    if curr == 26 {
        return 0
    }
    // calculate distance based on x,y coordinates in keyboard
    x1, y1 := curr / 6, curr % 6
    x2, y2 := dest / 6, dest % 6
    // get distance from finger position to key
    return abs(x1 - x2) + abs(y1 - y2)
}
```
