# 518. Coin Change 2 (Medium)

You are given `coins` of different denominations and a total `amount` of money. Write a function to
compute the number of combinations that make up that amount. You may assume that you have infinite
number of each kind of coin.

- Example 1:
> **Input**: amount = 5, coins = [1, 2, 5] <br>
> **Output**: 4 <br>
> **Explanation**: the ways are 5, 2+2+1, 2+1+1+1, 1+1+1+1+1

## Optimal - DP bottom-up
We use a `bottom-up` dynamic programming approach. We loop over the coins, and inside the loop we
check the amounts from *coin* to *amount*. The current solution expands the solution of the
remainder. I.e., **dp[j] += dp[j - coin]**. But why? We have already calculated *dp[j]* for the 
previous *coin*, so by using the new coin for *amount[j]*, we have the same solutions as before,
plus the solutions possible if we take the new coin, i.e. *dp[j - coin]*. 

Take the example below, when we start with coin=3 (and amount=3), we know that dp[3] = 1 (one way to
make the change with coins=1), then, by using the coin=3 we're expanding the solution by adding the
ways to make *dp[3-3] = dp[0]*. This means that if we use the coin of 3, we have to check how many
ways we can do 0 (already calculated).

amount | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7
--- | --- | --- | --- | --- | --- | --- | --- | ---
coin (1) | 1 | **1** | 1 | 1 | 1 | 1 | 1 | 1
coin (3) | 1 | 1 | 1 | **2** | 2 | 2 | 3 | 3
coin (5) | 1 | 1 | 1 | 2 | 2 | **3** | 4 | 4

**Time: O(A\*C)** (where A -> amount and C -> number of coins) <br> **Space: O(A)**

### Go solution
```go
func change(amount int, coins []int) int {
    dp := make([]int, amount + 1)
    dp[0] = 1 // base case, there's only one way to make 0 amount: 0 coins
    for _, coin := range coins {
        for j := coin; j <= amount; j++ {
            rem := j - coin
            dp[j] += dp[rem]
        }
        fmt.Println(dp)
    }
    return dp[amount]
}
```
### Java solution
```java
class Solution {
    public int change(int amount, int[] coins) {
        int cLen = coins.length;
        int[] dp = new int[amount+1];
        dp[0] = 1; // there's one way to make amount 0 with 0 coins
        for(int i = 0; i < cLen; i++) {
            int coin = coins[i];
            for(int j = coin; j <= amount; j++) { // start from coin
                int rem = j - coin;
                dp[j] += dp[rem]; // coin extends the current solution plus the solutions on dp[rem]
            }
        }
        return dp[amount];
    }
}
```