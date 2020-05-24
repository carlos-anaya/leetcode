# 239. Sliding Window Maximum (Hard)

Given an array `nums`, there is a *sliding window* of size `k` which is moving from the very left of
the array to the very right. You can only see the `k` numbers in the window. Each time the sliding
window moves right by one position. Return the max sliding window.

**Follow up**: Could you solve it in linear time?

- Example 1:
> **Input**: nums = [1,3,-1,-3,5,3,6,7], and k = 3 <br>
> **Output**: [3,3,5,5,6,7] <br>
> **Explanation**: See the table below

Window position | Max
--- | ---
**[1  3  -1]** -3  5  3  6  7 | 3
 1 **[3  -1  -3]** 5  3  6  7 | 3
 1  3 **[-1  -3  5]** 3  6  7 | 5
 1  3  -1 **[-3  5  3]** 6  7 | 5
 1  3  -1  -3 **[5  3  6]** 7 | 6
 1  3  -1  -3  5 **[3  6  7]** | 7

## Brute Force
Nested loop: loop in each window to find the greatest value. Outer loop moves the window and inner
loop finds the greatest value.

We can't just maintain the *max* and compare against the new number since that *max* number might
not be in the window anymore.

**Time: O(n\*k) <br> Space: O(1)**

## Optimal I
Use a `monotonic max queue`. The *monotonic max queue* will hold a decreasing subsequence given the
numbers in the window. The **max** of the queue will always be at the **head**. When the window
moves, we push the new number *nums[i]* in the queue (tail) and remove the last number in the window
 *nums[i-k+1]* (if it exist in the queue, i.e. if it is the current max). See the example below:

Window position | Max | Monotonic Max Queue | Action
--- | --- | --- | ---
**[1  3  -1]** -3  5  3  6  7 | 3 | [3,-1] | push(1), push(3), push(-1)
 1 **[3  -1  -3]** 5  3  6  7 | 3 | [3,-1,-3] | pop(1), push(-3)
 1  3 **[-1  -3  5]** 3  6  7 | 5 | [5] | pop(3), push(5)
 1  3  -1 **[-3  5  3]** 6  7 | 5 | [5,3] | pop(-1), push(3)
 1  3  -1  -3 **[5  3  6]** 7 | 6 | [6] | pop(-3), push(6)
 1  3  -1  -3  5 **[3  6  7]** | 7 | [7] | pop(5), push(7)

Why does this work? The monotonic queue will always hold the max of the current window because if a
new max arrives it will *"pop"* the existing values in the queue. If no new max arrives, then when
we're at index i+k+1 the current max will be popped because it is the last element in the window.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func maxSlidingWindow(nums []int, k int) []int {
    mmq := NewMonotonicMaxQueue()
    ans := make([]int, 0, len(nums)-k+1)
    
    for i, num := range nums {
        // not enough numbers in the window
        if i < k {
            mmq.Push(num)
            continue
        }
        // add the max of the curr window to the ans
        ans = append(ans, mmq.Max())
        // move the window - add new num and remove num[i-k]
        mmq.Push(num)
        mmq.Pop(nums[i-k])
    }
    // add the max of the last window to the ans
    ans = append(ans, mmq.Max())
    
    return ans
}

type MonotonicMaxQueue struct {
    arr []int
}

func NewMonotonicMaxQueue() MonotonicMaxQueue {
    arr := make([]int, 0, 10)
    return MonotonicMaxQueue{arr}
}

func (m *MonotonicMaxQueue) Push(num int) {
    // "pop" elements smaller than num, so it maintains 
    // a monotonic decreasing sequence
    for len(m.arr) > 0 && m.arr[len(m.arr)-1] < num {
        m.arr = m.arr[:len(m.arr)-1]
    }
    m.arr = append(m.arr, num)
}

func (m *MonotonicMaxQueue) Pop(num int) {
    if len(m.arr) > 0 && m.arr[0] == num {
        m.arr = m.arr[1:]
    } 
}

func (m *MonotonicMaxQueue) Max() int {
    if len(m.arr) == 0 {
        return -1
    }
    return m.arr[0]
}
```
## Optimal II
Instead of using a `monotonic max queue` implementation we can use a regular **deque** or a 
**doubly linked list** to simulate the same behaviour.

**Time: O(n) <br> Space: O(n)**

### Java solution
```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        Deque<Integer> q = new ArrayDeque();
        int[] ans = new int[nums.length-k+1];
        int j = 0;
        for(int i=0; i < nums.length; i++) {
            int num = nums[i];
            // not enough chars in window just add the number to the queue
            if (i < k) {
                addToDeque(q, num);
                continue;
            }
            // head of the queue is the max of the window
            ans[j] = q.getFirst();
            j++;
            // pop head if it is the same as the num removed from the window
            if(q.getFirst() == nums[i-k])
                q.removeFirst();
            // add the new window number to the queue
            addToDeque(q, num);
        }
        // add max of last window
        ans[j] = q.getFirst();
        return ans;
    }
    // adds to the tail of the queue, maintaining a decreasing monotonic sequence
    private void addToDeque(Deque<Integer> q, int num) {
        while(!q.isEmpty() && q.getLast() < num)
            q.removeLast();
        q.add(num);
    }
}
```