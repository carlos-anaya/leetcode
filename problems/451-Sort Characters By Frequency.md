# 451. Sort Characters By Frequency

Given a `string`, *sort* it in decreasing order based on the frequency of characters.

- Example 1:
> **Input**: "tree" <br>
> **Output**: "eert" <br>
> **Explanation**: "e" appears twice, so it comes first. "eetr" is also valid
- Example 2:
> **Input**: "IreallyloveLeetcode" <br>
> **Output**: "eeeeelllooILacdrtvy" <br>
> **Explanation**: Note that 'l' and L are considered different. 

## Approach - Priority Queue
Use a **priority queue** or **max heap**. First create the frequency map/array and then add those
values (as map entries) to the pq/max heap, then just *poll* the entries from queue.

**Time: O(n\*logn) <br> Space: O(n)**

## Approach - Bucket Sort
Instead of a **priority queue** or a **max heap** we can use a bucket sort. We keep track of the
*max frequency* and then create the buckets. Next, we loop over the char array/map and put the char
in the appropriate *frequency* bucket. Finally, we loop over the buckets and get the chars on that
bucket.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func frequencySort(s string) string {
    // assume ASCII chars only
    charCount := make([]int, 256)
    maxCount := 0
    // count occurrences of each char
    for _, c := range s {
        charCount[int(c)]++
        maxCount = max(maxCount, charCount[int(c)])
    }
    // create "maxCount" buckets
    // then add each char in the correct count bucket
    buckets := make([][]rune, maxCount + 1)
    for i, count := range charCount {
        if count > 0 {
            buckets[count] = append(buckets[count], rune(i))
        }
    }
    // loop over the buckets and add each char "count" times
    ans := make([]rune, 0, len(s))
    for count := maxCount; count > 0; count-- {
        for _, c := range buckets[count] {
            for j := 0; j < count; j++ {
                ans = append(ans, c)
            }
        } 
    }
    return string(ans)
}
```
### Java solution
```java
class Solution {
    public String frequencySort(String s) {
        int[] freq = new int[256];
        int maxFreq = 0;
        // count frequencies of each char
        for(int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            freq[c]++;
            maxFreq = Math.max(maxFreq, freq[c]);
        }
        // create "maxFreq" buckets and initialize it with empty lists
        List<Character>[] buckets = new List[maxFreq + 1];
        for(int i = 1; i <= maxFreq; i++)
            buckets[i] = new ArrayList();
        // loop over chars and add them to the appropriate bucket
        for(char c = 0; c < 256; c++) {
            if(freq[c] > 0) 
                buckets[freq[c]].add(c);
        }
        // loop over buckets, then over chars in bucket and append char "freq" times
        StringBuilder sb = new StringBuilder();
        for(int f = maxFreq; f > 0; f--) {
            for(char c : buckets[f]) {
                for(int i = 0; i < f; i++) {
                    sb.append(c);
                }
            }
        }
        return sb.toString();
    }
}
```