# 1029. Two City Scheduling (Easy)

There are `2N` people a company is planning to interview. The cost of flying the `i-th` person to 
**city A** is costs[i][0], and the cost of flying the `i-th` person to **city B** is costs[i][1].

Return the **minimum cost** to fly every person to a city such that exactly `N` people arrive in
each city.

- Example 1:
> **Input**: [[10,20],[30,200],[400,50],[30,20]] <br>
> **Output**: 110 <br>
> **Explanation**:The first person goes to city A for a cost of 10. <br>
> The second person goes to city A for a cost of 30. <br>
> The third person goes to city B for a cost of 50. <br>
> The fourth person goes to city B for a cost of 20. <br>
> The total minimum cost is 10 + 30 + 50 + 20 = 110.

## Optimal
We use a `greedy approach`. But first we need to sort the input in an ideal way. How is that?
The company needs to send all the people to any of the two cities. Let's assume we want to find the
best choices to send to `cityA`. E.g. consider *person_0*: [10,20], so, if we send him to `cityA`,
the company would *save* 10 (20 - 10 = 10). Now consider *person_1*: [400,50], so if we send him to
`cityA`, the company would *loss* 350 (50 - 400 = -350).

So, if we sort the persons by the savings/loss to send them to `cityA`, we will have the best
choices at the beginning and the wost choices at the end. But the worst choices for `cityA` are the
best choices for `cityB`!. So, given that order, the first half goes to `cityA` and the others to
`cityB`.

- e.g. costs [[10,20],[400,50],[50,100],[30,200],[30,20],[60,50]]

- sorted costs: 

sorted array| [30 200] | [50 100] | [10 20] | [30 20] | [60 50] | [400 50]]
--- | --- | --- | --- | --- | --- | ---
diff | -170 | -50 | -10 | 10 | 10 | 350


![href two cities](https://leetcode.com/problems/two-city-scheduling/Figures/1029/users.png)

**Time: O(n\*logn) <br> Space: O(1)**

### Go solution
```go
func twoCitySchedCost(costs [][]int) int {
  // sort by difference of sending person 1 vs person 2 to city A
    sort.Slice(costs, func(i, j int) bool {
        return (costs[i][0]-costs[i][1]) < (costs[j][0]-costs[j][1])
    })
    
    // array is already sorted, so send the first half to 
    // city A and the rest to city B
    n, cost := len(costs), 0
    for i := 0; i < n/2; i++ {
        cost += costs[i][0]
        cost += costs[n-i-1][1]
    }
    
    return cost
}
```
### Java solution
```java
class Solution {
    public int twoCitySchedCost(int[][] costs) {
        // sort by difference of sending person 1 vs person 2 to city A
        Arrays.sort(costs, (a, b) -> Integer.compare(a[0] - a[1], b[0] - b[1]));
        int n = costs.length;
        
        // array is already sorted, so send the first half to 
        // city A and the rest to city B
        int cost = 0;
        for(int i = 0; i < n/2; i++) {
            cost += costs[i][0];
            cost += costs[n-i-1][1];
        }
        
        return cost;
    }
}
```