# 122. Best Time to Buy and Sell Stock II

Say you have an array for which the *i-th* element is the price of a given stock on day *i*.
Design an algorithm to find the maximum profit. You may complete as many transactions as you like 
(i.e., buy one and sell one share of the stock multiple times).

**Note*: You may not engage in multiple transactions at the same time 
(i.e., you must sell the stock before you buy again).

- Example 1:
> **Input**: [7,1,5,3,6,4] <br>
> **Output**: 7 <br>
> **Explanation**: Buy on day 2 (price = 1) and sell on day 3 (price = 5), profit = 5-1 = 4. 
> Then buy on day 4 (price = 3) and sell on day 5 (price = 6), profit = 6-3 = 3.
- Example 2:
> **Input**: [1,2,3,4,5] <br>
> **Output**: 4 <br>
> **Explanation**: Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5-1 = 4.
- Example 3:
> **Input**: [7,5,3,2,2] <br>
> **Output**: 0 <br>
> **Explanation**: Make no transactions since there will be no profit

## Optimal
Consider the following diagram. Everytime we find a greater value, the difference is a profit. As
long as we find a higher price, we keep increasing the profit. This way we discard the valleys and
just keep increasing the profit if there's a higher price.

Buying at point *A* and selling at point *D* is the same as buying at *A* selling at *B*, buying at
*B* selling at *C* and buying at *C*, selling at *D*.

![alt prices](https://leetcode.com/media/original_images/122_maxprofit_2.PNG)

**Time: O(n) <br> Space: O(1)**

### Go solution
```go
func maxProfit(prices []int) int {    
    profit := 0
    for i := 1; i < len(prices); i++ {
        if prices[i] > prices[i - 1] {
            profit += prices[i] - prices[i - 1]
        }
    }
    
    return profit
}
```
### Java solution
```java
class Solution {
    public int maxProfit(int[] prices) {
        if(prices.length < 2)
            return 0;
            
        int maxProfit = 0;
        for(int i = 1; i < prices.length; i++) {
            int diff = prices[i] - prices[i-1];
            if(diff > 0)
                maxProfit += diff;
        }
        
        return maxProfit;
    }
}
```