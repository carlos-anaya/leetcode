# 347. Top K Frequent Elements (Medium)

Given a *non-empty* array of integers, return the **k** most frequent elements.

**Note**:
- You may assume *k* is always valid, 1 ≤ k ≤ number of unique elements.
- Your algorithm's time complexity must be better than *O(n log n)*, where *n* is the array's size.
- It's guaranteed that the answer is unique, i.e. the set of the top k frequent elements is unique.
- You can return the answer in *any order*.

- Example 1:
> **Input**: nums = [1,1,1,2,3,2,1], k = 2 <br>
> **Output**: [1,2]
> **Explanation**: *1* appears 4 times, and *2* appears twice

## Brute Force
We can use a `max heap` to keep track of the most frequent elements. First we create a map with the
frequencies, then a heap of capacity *k* and we fill it with the values from the map. Finally, we
can just poll elements from the heap into an *answer array*.

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap();
        // fill frequencies map
        for(int num : nums)
            map.put(num, map.getOrDefault(num, 0) + 1);
        
        // create maxHeap of capacity k and fill it
        PriorityQueue<Map.Entry<Integer, Integer>> maxHeap = 
            new PriorityQueue<>(k, (a,b)->Integer.compare(b.getValue(), a.getValue()));
        for(Map.Entry<Integer, Integer> pair : map.entrySet())
            maxHeap.add(pair);
        
        // poll items from heap
        int[] ans = new int[k];
        for(int i = 0; i < k; i++) {
            ans[i] = maxHeap.poll().getKey();
        }
        
        return ans;
    }
}
```

**Time: O(n\*logk) <br> Space: O(n)**

## Optimal
Use a **bucket sort**. First store in a map the numbers and their frequencies. Keep track of the
*max frequency* in order to create the necessary buckets. Then, loop over the map *<num, freq>*
pairs and add the *num* in the corresponding *bucket*. Finally, get *k* nums from the buckets,
starting from the last bucket (most frequent nums).

**Time: O(n)** (building the map and putting nums in buckets)<br> 
**Space: O(n)** (the number of buckets will be at most n)

### Go solution
```go
func topKFrequent(nums []int, k int) []int {
    countMap := make(map[int]int)
    maxCount := 1
    // fill frequencies map
    for _, num := range nums {
        if count, found := countMap[num]; found {
            countMap[num] = count + 1
            maxCount = max(maxCount, count + 1)
        } else {
            countMap[num] = 1
        } 
    }
    // put numbers in buckets per count
    buckets := make([][]int, maxCount+1)
    for num, count := range countMap {
        buckets[count] = append(buckets[count], num)
    }
    // get k frequent numbers
    ans := make([]int, 0, k)
    for i := maxCount; i > 0; i-- {
        ans = append(ans, buckets[i]...)
        if len(ans) == k {
            return ans
        }
    }
    return ans
}
```
### Java solution
```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap();
        int maxFreq = 0;
        // fill frequencies map
        for(int num : nums) {
            int freq = map.getOrDefault(num, 0) + 1;
            map.put(num, freq);
            maxFreq = Math.max(maxFreq, freq);
        }
        // create bucket list and add pairs
        List<List<Integer>> buckets = new ArrayList();
        for(int i = 0; i <= maxFreq; i++)
            buckets.add(new ArrayList());
        for(Map.Entry<Integer, Integer> pair : map.entrySet()) {
            buckets.get(pair.getValue()).add(pair.getKey());
        }
        // get k elements from the buckets, starting from last bucket
        int i = 0;
        int[] ans = new int[k];
        for(int j = maxFreq; j > 0; j--) {
            for(int num : buckets.get(j)) {
                ans[i] = num;
                i++;
                if(i == k) // got the most frequent k nums
                    return ans;
            }
        }
        return ans;
    }
}
```