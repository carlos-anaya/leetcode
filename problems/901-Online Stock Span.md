# 901. Online Stock Span (Medium)

Write a class `StockSpanner` which collects daily price quotes for some stock, and returns the span
of that stock's price for the current day.

The span of the stock's price today is defined as the maximum number of consecutive days (starting
from today and going backwards) for which the price of the stock was 
**less than or equal to today's price**.

- Example:
> **Input**: [100, 80, 60, 70, 60, 75, 85] <br>
> **Output**: [1, 1, 1, 2, 1, 4, 6] <br>
> **Explanation**: <br>
> Note that (for example) S.next(75) returned 4, because the last 4 prices <br>
> (including today's price of 75) were less than or equal to today's price.

## Brute Force
Store the stock prices in an array. When a new stock comes, check the array backwards for finding
a higher price.

**Next operation: Time: O(n) <br> Space: O(1)**

## Optimal
Instead of checking all the pricess for each new stock, we can reuse she knowledge of previous
stock prices and their spans. We use a `stack` with a pair `<price, span>`. For a new stock we
create a new Tuple with a span of *1*. Then we *pop* elements from the stack as long as they're 
*smaller or equal* than current price and add those spans to the *current tuple*. 

**Next operation: Time: O(1) amortized <br> Space: O(n)**

### Go solution
```go
type Tuple struct {
    price, span int
}

type StockSpanner struct {
    stocks []*Tuple
}

func Constructor() StockSpanner {
    stocks := make([]*Tuple, 0, 10000)
    return StockSpanner{stocks}
}

func (this *StockSpanner) Next(price int) int {
    t := &Tuple{price, 1}
    for {
        peekIdx := len(this.stocks) - 1
        // "stack" is empty or new price is lower than peek
        if peekIdx < 0 || this.stocks[peekIdx].price > price { 
            break 
        }
        t.span += this.stocks[peekIdx].span;
        // "pop" the "peek"
        this.stocks = this.stocks[:peekIdx]
    }
    this.stocks = append(this.stocks, t)

    return t.span
}
```
### Java solution
```java
class StockSpanner {
    class Tuple {
        int price, span;
        Tuple(int price, int span) {
            this.price = price;
            this.span = span;
        }
    }
    
    Stack<Tuple> stocks;
    
    public StockSpanner() {
        stocks = new Stack();
    }
    
    public int next(int price) {
        Tuple t = new Tuple(price, 1);
        while(!this.stocks.empty() && this.stocks.peek().price <= price) {
            t.span += this.stocks.pop().span;
        }
        this.stocks.push(t);
        
        return t.span;
    }
}
```