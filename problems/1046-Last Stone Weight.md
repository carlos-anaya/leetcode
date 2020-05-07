# 1046. Last Stone Weight

We have a *collection of stones*, each stone has a positive integer weight.

Each turn, we choose the two heaviest stones and smash them together.  Suppose the stones have 
weights *x* and *y* with *x <= y*.  The result of this smash is:

- If x == y, both stones are totally destroyed;
- If x != y, the stone of weight *x* is destroyed, and the stone of weight *y* has new weight *y-x*

At the end, there is at most 1 stone left. Return the weight of this stone (or 0 if there are no 
stones left.)

- Example 1:
> **Input**: [2,7,4,1,8,1] <br>
> **Output**: 1 <br>
> **Explanation**: We combine 7 and 8 to get 1 so the array converts to [2,4,1,1,1] then, <br>
> we combine 2 and 4 to get 2 so the array converts to [2,1,1,1] then, <br>
> we combine 2 and 1 to get 1 so the array converts to [1,1,1] then, <br>
> we combine 1 and 1 to get 0 so the array converts to [1] then that's the value of last stone.

## Brute Force I
Loop and sort. Inside the loop *sort* the array, then take the top 2 and set
*arr[i] = arr[i] - arr[i-1]*, set *arr[i-1]=0*. Repeat until there's just one element.

**Time: O(n²logn) <br> Space: O(1)**

## Brute Force II
Nested loop. Find *max 1* and *max 2*. Set *arr[m1] = arr[m1] - arr[m2]*, set *arr[m2]=0*.
Repeat until there's just one element.

**Time: O(n²) <br> Space: O(1)**

## Optimal
Use a `Max heap` in Go or a `Priority Queue` in Java (Use **Comparator.reverseOrder()** since by
default the priority queue will be a *min heap*).
- Insert all the stones in the heap
- Loop over the heap until there's one element left. Take the top two and add the difference back
to the heap.

**Time: O(nlogn)** (*n* items and *log n* time for insertion/removal of the heap/pq)<br>
**Space: O(n)** (all the elements in the heap/pq)

### Go solution
```go
func lastStoneWeight(stones []int) int {
    heap := NewMaxHeap(len(stones))
    
    for _, stone := range stones {
        heap.Insert(stone)
    }
    
    for heap.Size() > 1 {
        heap.Insert(heap.GetMax() - heap.GetMax())
    }
    
    return heap.Peek()
}
```
### Java solution
```java
class Solution {
    public int lastStoneWeight(int[] stones) {
        PriorityQueue<Integer> maxHeap = new PriorityQueue(Comparator.reverseOrder());
        
        for(int stone : stones) {
            maxHeap.offer(stone);
        }
        
        while(maxHeap.size() > 1) {
            maxHeap.offer(maxHeap.poll() - maxHeap.poll());
        }
        
        return maxHeap.peek();
    }
}
```