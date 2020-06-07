# 322. Coin Change (Medium)

You are given `coins` of different denominations and a total amount of money `amount`. Write a
function to compute the **fewest number of coins** that you need to make up that amount. If that
amount of money cannot be made up by any combination of the coins, return -1.

- Example 1:
> **Input**: coins = [1, 2, 5], amount = 11 <br>
> **Output**: 3 <br>
> **Explanation**: 11 = 5 + 5 + 1
- Example 2:
> **Input**: coins = [3, 5], amount = 7  <br>
> **Output**: -1 

## Brute Force - Recursion & Memorization
We can do a recursive function and use a `memo` array to keep track of previous computed values.
In the memo array we store the number of coins needed for an amount *i*.

The base case of the recursion is when there's a valid combination (amount remaining = 0) or an
invalid combination (amount remaining < 0, i.e. no way to make the exact amount with these coins).

We loop over the coins and check whether they yield a valid combination. In case the combination is
valid, we keep track of the best solution so far (min number of coins).

**Time: O(A\*C)** (where A -> amount and C -> number of coins) <br> **Space: O(A)**

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int[] memo = new int[amount + 1];
        return coinChange(coins, amount, memo);
    }
    
    private int coinChange(int[] coins, int amount, int[] memo) {
        if(amount == 0) // valid combination
            return 0;
        if(amount < 0) // invalid combination
            return -1;
        if(memo[amount] != 0) // previously computed coins for amount
            return memo[amount];
        
        int minCoins = Integer.MAX_VALUE;
        for(int coin : coins) {
            int usedCoins = coinChange(coins, amount-coin, memo);
            // this combination is valid and it takes less coins
            if(usedCoins >= 0 && usedCoins < minCoins)
                minCoins = usedCoins + 1;
        }
        memo[amount] = minCoins == Integer.MAX_VALUE ? -1 : minCoins;
        return memo[amount];
    }
}
```

## Optimal - DP bottom-up
Similar to the approach above we use a *memo/dp* array. We loop over the coins, checking if they
can make a valid combination for a given amount. If so, the *number of coins* needed are 
*1 plus the number of coins needed to make amount-coins*. This is: **dp[j] = dp[j - coin]**.

But, since we need the minimum value, at each step we get the min value from the current number of
coins and the previously computed number of coins: **dp[j] = min(dp[j], dp[j - coin] + 1)**.

We just need to initialize the array to a large value (amount + 1), so any new valid num of coins
will update the *dp* array. e.g.: (amount is 11, so we initialize the dp to *12*).

amount | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11
--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | ---
coin (2) | 0 | *12* | 1 | *12* | 2 | *12* | 3 | *12* | 4 | *12* | 5 | *12*
coin (3) | 0 | *12* | 1 | 1 | 2 | 2 | 2 | 3 | 3 | 3 | 4 | 4
coin (5) | 0 | *12* | 1 | 1 | 2 | 1 | 2 | 2 | 2 | 3 | 2 | 3

**Time: O(A\*C)** (where A -> amount and C -> number of coins) <br> **Space: O(A)**

### Go solution
```go
func coinChange(coins []int, amount int) int {
    dp := make([]int, amount + 1)
    // initialize dp with "amount + 1", so any number of coins for a 
    // valid combination would be lesser than "amount + 1"
    // dp[0] = 0 -> base case, since amount 0 requires 0 coins
    for i := 1; i <= amount; i++ {
        dp[i] = amount + 1
    }
    for _, coin := range coins {
        for j := coin; j <= amount; j++ {
            // take the min of the current coins for the amount
            // or using this coin and the number of coins for amount-coin
            dp[j] = min(dp[j], dp[j - coin] + 1)
        }
        fmt.Println(dp)
    }
    if dp[amount] < amount + 1 {
        return dp[amount]
    }
    return -1
}
```
