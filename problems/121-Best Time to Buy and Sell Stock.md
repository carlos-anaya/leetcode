# 121. Best Time to Buy and Sell Stock

Say you have an array for which the *i-th* element is the price of a given stock on day *i*.

If you were only permitted to complete at most one transaction (i.e., buy one and sell one share of
the stock), design an algorithm to find the maximum profit.

**Note**: that you cannot sell a stock before you buy one.

- Example 1:
> **Input**: [7,1,5,3,6,4] <br>
> **Output**: 5 <br>
> **Explanation**: buy at day 1 (price 1) and sell at day 4 (price 6).
- Example 2:
> **Input**: [7,6,4,3,1] <br>
> **Output**: 0 <br>
> **Explanation**: no transaction is made since the profit would be negative.

## Brute Force
Nested loop: for each price, loop over the prices and check if any difference is better than 
*maxProfit*.

**Time: O(n²) <br> Space: O(1)**

## Optimal
Loop through the prices and check if *currPrice* (idx=i) minus the *minPrice* so far is better than
*maxProfit*. Keep updating *minPrice* if any cheaper price found. Start checking prices at day 1,
and initialize *minPrice* with price at day 0.

**Time: O(n) <br> Space: O(1)**

### Go solution
```go
func maxProfit(prices []int) int {
    if len(prices) < 2 {
        return 0
    }
    maxProfit := 0
    minPrice := prices[0]
    for i := 1; i < len(prices); i++ {
        maxProfit = max(maxProfit, prices[i] - minPrice)
        minPrice = min(minPrice, prices[i])
    }
    
    return maxProfit
}
```
### Java solution
```java
class Solution {
    public int maxProfit(int[] prices) {
        if(prices.length < 2)
            return 0;
        int minPrice = prices[0], maxProfit = 0;
        for(int i = 1; i < prices.length; i++) {
            maxProfit = Math.max(maxProfit, prices[i] - minPrice);
            minPrice = Math.min(minPrice, prices[i]);
        }
        
        return maxProfit;
    }
}
```