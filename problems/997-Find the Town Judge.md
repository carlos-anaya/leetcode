# 997. Find the Town Judge (Easy)

In a town, there are `N` people labelled from 1 to N. There is a rumor that one of these people is
secretly the town judge. If the town judge exists, then:

1. The town judge trusts nobody.
2. Everybody (except for the town judge) trusts the town judge.
3. There is exactly one person that satisfies properties 1 and 2.

You are given `trust`, an array of pairs `trust[i] = [a, b]` representing that the person labelled 
*a* trusts the person labelled *b*. If the town judge exists and can be identified, return the label
of the town judge. Otherwise, return -1.

- Example 1:
> **Input**: N = 2, trust = [[1,2]] <br>
> **Output**: 2 <br>
> **Explanation**: 1 trusts 2 and 2 trusts nobody
- Example 2:
> **Input**: N = 3, trust = [[1,3],[2,3]] <br>
> **Output**: 3 <br>
> **Explanation**: 1, 2 trust 3 and 3 trusts nobody
- Example 3:
> **Input**: N = 3, trust = [[1,3],[2,3],[3,1]] <br>
> **Output**: -1 <br>
> **Explanation**: 1, 2 trust 3 but 3 trusts 1
- Example 4:
> **Input**: N = 3, trust = [[1,2],[2,3]] <br>
> **Output**: -1 <br>
> **Explanation**: 3 is a candidate but it's not trusted by everyone (1 missing)
- Example 5:
> **Input**: N = 4, trust = [[1,3],[1,4],[2,3],[2,4],[4,3]] <br>
> **Output**: 3 <br>
> **Explanation**: All people trust 3 and 3 trusts nobody

## Optimal
We are given *trusts* which represent **edges**, and *N* is the number of people, in this case the
**vertices**. The town judge trust nobody and everybody trusts the town judge means, in term of a
graph, that the *town judge vertix* has **0 out-degree** and **N-1 in-degree**.

We can have two arrays, fon *in* and *out* degrees and then check if any index (vertix) meets the
requirement. Furthermore, instead of two arrays, we can just have one, *incrementing* the count for
out-degrees, and *decrementing* the count for in-degrees. Then just check if any index has a 
*count == N-1*.

**Time: O(T+P)** (loop over the Trusts (edges) and then loop over the People (vertices)) <br> 
**Space: O(P)** (array of size P)

### Go solution
```go
func findJudge(N int, trust [][]int) int {
    people := make([]int, N+1)
    for _, t := range trust {
        people[t[0]]--
        people[t[1]]++
    }
    for i := 1; i <= N; i++ {
        if people[i] == N - 1 {
            return i
        }
    }
    return -1
}
```
### Java solution
```java
class Solution {
    public int findJudge(int N, int[][] trust) {
        int[] people = new int[N+1];
        for(int[] t : trust) {
            people[t[0]]--;
            people[t[1]]++;
        }
        for(int i = 1; i <= N; i++)
            if(people[i] == N - 1)
                return i;
        
        return -1;
    }
}
```