# 277 Find the Celebrity (Medium)

Suppose you are at a party with `n` people (labeled from `0 to n - 1`) and among them, there may
exist one *celebrity*. The definition of a *celebrity* is that all the other n - 1 people know
him/her but he/she does not know any of them.

Now you want to find out who the celebrity is or verify that there is not one. The only thing you
are allowed to do is to ask questions like: *"Hi, A. Do you know B?"* to get information of whether
*A knows B*. You need to find out the celebrity (or verify there is not one) by asking as few
questions as possible (in the asymptotic sense).

You are given a helper function `bool knows(a, b)` which tells you whether A knows B. Implement a
function int findCelebrity(n), your function should minimize the number of calls to knows.

**Note:** There will be exactly one celebrity if he/she is in the party. Return the celebrity's
label if there is a celebrity in the party. If there is no celebrity, return -1.

- Example 1:
> **Input**: 3 <br>
> knows(0, 1) -> false <br>
> knows(0, 2) -> false <br>
> knows(1, 0) -> true <br>
> knows(1, 2) -> false <br>
> knows(2, 0) -> true <br>
> knows(2, 1) -> true <br>
> **Output**: 0 <br>
> **Explanation**: Everyone knows 0, and 0 knows nobody

## Brute Force
Nested loop. For each person check if he/she knows all the other people. Keep a count of 
*in-degrees* adding *1* when somebody knows him/her, or *-1* when she/he knows somebody; then at the
end check if there's a person with *n-1 in-degree* (everybody knows him/her) and he/she knows nobody

**Time: O(n²) <br> Space: O(1)**

```java
public class Solution extends Relation {
    public int findCelebrity(int n) {
        int[] people = new int[n];
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < n; j++) {
                if(j == i)
                    continue;
                if(knows(i, j)) {
                    people[i]--;
                    people[j]++;
                }
            }
        }
        for(int i = 0; i < n; i++) {
            if(people[i] == n-1)
                return i;
        }
        return -1;
    }
}
```

## Approach I - Stack
Instead of checking all the people for a given candidate, we should be able to re-use that knowledge
and avoid candidates previously discarded. 

We can use a `stack` to keep track of the candidates. If *A* knows *B*, then *A* is no longer a
candidate and we need to keep *B*.  If *A* doesn't know *B*, then *B* is no longer a candidate and
*A* is. Keep doing the process until we're left with just one candidate. Finally, check if the
*final candidate* is known by everybody and he/she knows nobody. Why? We need this step to ensure
it is not a false positive. He/she might not be known by everybody or he/she might know someone.

**Time: O(n) <br> Space: O(n)**

### Java solution
```java
public class Solution extends Relation {
    public int findCelebrity(int n) {
        Stack<Integer> candidates = new Stack();
        for(int i = 0; i < n; i++)
            candidates.push(i);
        // discard people that knows other people
        while(candidates.size() > 1) {
            int p1 = candidates.pop(), p2 = candidates.pop();
            if(knows(p1, p2))
                candidates.push(p2); // p2 is a candidate
            else
                candidates.push(p1); // p1 is a candidate
        }
        // check if candidate is the celebrity
        int finalCandidate = candidates.pop();
        for(int i = 0; i < n; i++) {
            if(finalCandidate == i) // skip himself/herself
                continue;
            if(knows(finalCandidate, i) || !knows(i, finalCandidate))
                return -1;
        }
        return finalCandidate;
    }
}
```

## Optimal - Two pointers
Instead of using a *stack*, we can use the `two pointer` approach to find a *final candidate*. We
have a pointer for *person1* and other for *personN*. If *person1* knows *personN*, then *person1*
is not a candidate, so we move to *person2*. Otherwise, *personN* is not a candidate, so we move to
*personN-1*. We continue the loop until we have a final candidate. Finally, we do the final check
as mentioned before.

**Time: O(n) <br> Space: O(1)**

```java
public class Solution extends Relation {
    public int findCelebrity(int n) {
        int l = 0, r = n-1;
        // discard people that knows other people
        while(r > l) {
            if(knows(l, r))
                l++; // r is a candidate, so skip l
            else
                r--; // l is a candidate, so skip r
        }
        // r == l, so check if candidate is the celebrity
        for(int i = 0; i < n; i++) {
            if(l == i) // skip himself/herself
                continue;
            if(knows(l, i) || !knows(i, l))
                return -1;
        }
        return l;
    }
}
```