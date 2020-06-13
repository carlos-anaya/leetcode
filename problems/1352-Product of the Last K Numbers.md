# 1352. Product of the Last K Numbers (Medium)

Implement the class `ProductOfNumbers` that supports two methods:
- `add(int num)` - Adds the number num to the back of the current list of numbers.
- `getProduct(int k)` - Returns the product of the last k numbers in the current list.

**Notes**:
- You can assume that always the current list has at least k numbers.
- At any time, the product of any contiguous sequence of numbers will fit into a single 32-bit 
  integer without overflowing.

- Example 1:
> **Input**: <br>
> ["ProductOfNumbers","add","add","add","add","add","getProduct","getProduct","getProduct","add","getProduct"] <br>
[[],[3],[0],[2],[5],[4],[2],[3],[4],[8],[2]]
> **Output**: <br>
> [null,null,null,null,null,null,20,40,0,null,32]
> **Explanation**: <br>
> ProductOfNumbers productOfNumbers = new ProductOfNumbers(); <br>
> productOfNumbers.add(3);        // [3] <br>
> productOfNumbers.add(0);        // [3,0] <br>
> productOfNumbers.add(2);        // [3,0,2] <br>
> productOfNumbers.add(5);        // [3,0,2,5] <br>
> productOfNumbers.add(4);        // [3,0,2,5,4] <br>
> productOfNumbers.getProduct(2); // return 20. The product of the last 2 numbers is 5 * 4 = 20 <br>
> productOfNumbers.getProduct(3); // return 40. The product of the last 3 numbers is 2 * 5 * 4 = 40 <br>
> productOfNumbers.getProduct(4); // return 0. The product of the last 4 numbers is 0 * 2 * 5 * 4 = 0 <br>
> productOfNumbers.add(8);        // [3,0,2,5,4,8] <br>
> productOfNumbers.getProduct(2); // return 32. The product of the last 2 numbers is 4 * 8 = 32 

## Brute Force
Keep an `array` for keeping track of the numbers, then for each `getProduct(k)` call compute the
product of numbers *from i to i-k* where *i* is the index of last element in the array.

**Time: O(n²)** (n -> number of elements. `add` is O(1), but `getProduct` is O(n)) <br> 
**Space: O(1)**

## Optimal
We can avoid *calculating the products for each `getProduct` function call. We keep an array not
with the numbers but with the cumulate *prods*, i.e. *prod[i] = num * prod[i-1]*. This way the last
element of the array has the cumulative prods of all the elements in the array. Then, when there's a
`getProduct` request, we simply return *prods[i]/prods[i-k]* where *i* is the index of last element
in the array.

Let's consider the input array: [2,3,5,8], then we will have *prods=[1,2,6,30,240]* (let's add 1 at 
the beginning for simplicity). For `getProduct(2)` we have 8\*5 = 40. Then, we have 
*prods[4]/prods[4-2]* = 240/6 = 40. *Why this works?* The cumulative prod at *i=4* is 2\*3\*5\*8,
and the cumulative prod at *i-2* is 2\*3, so the division leaves 5\*8.

Finally, in case of `0` we reset the array since all previous prods will be *zero*, so we can save
some space.

**Time: O(n)** (n -> number of elements. Both `add` and `getProduct` are O(1)) <br> **Space: O(n)**

### Go solution
```go
type ProductOfNumbers struct {
    prods []int
    count int
}

func Constructor() ProductOfNumbers {
    prods := make([]int, 0)
    prods = append(prods, 1)
    return ProductOfNumbers{prods, 1}
}


func (this *ProductOfNumbers) Add(num int)  {
    if num == 0 {
        this.prods = make([]int, 0)
        this.prods = append(this.prods, 1)
        this.count = 1
    } else {
        idx := this.count - 1
        this.prods = append(this.prods, num * this.prods[idx])
        this.count++
    }
}


func (this *ProductOfNumbers) GetProduct(k int) int {
    fmt.Println(this.prods)
    if k >= this.count {
        return 0
    }
    idx := this.count - 1
    return this.prods[idx] / this.prods[idx-k] 
}
```