# 278. First Bad Version

You are a product manager and currently leading a team to develop a new product. Unfortunately, the
latest version of your product fails the quality check. Since each version is developed based on the
previous version, all the versions after a bad version are also bad.

Suppose you have `n` versions [1, 2, ..., n] and you want to find out the first bad one, which
causes all the following ones to be bad.

You are given an API **bool isBadVersion(version)** which will return whether version is bad. 
Implement a function to find the 1st bad version. You must minimize the number of calls to the API.

- Example 1:
> **Input**: Given n = 5, and version = 4 is the first bad version. <br>
> **Output**: 4 

## Brute Force
Single loop for each version (from *i=1* to *n*). Return *i* when first bad version is found.

**Time: O(n) <br> Space: O(1)**

## Optimal
Use a `binary search`. When a good version is found at index *m*, refine the search from *l=m+1* to
*r*. If a bad version is found, refine the search from *l to r=m* (note that it's not *r=m-1* since
the bad version at *m* can be the first one, so we don't skip it). When *l==r* we have found the 
first bad version.

**Time: O(log n) <br> Space: O(1)**

### Go solution
```go
func firstBadVersion(n int) int {
    l, r := 1, n
    for l < r {
        m := (r - l)/2 + l
        if isBadVersion(m) {
            r = m
        } else {
            l = m + 1
        }
    }
    return l
}
```
### Java solution
```java
public class Solution extends VersionControl {
    public int firstBadVersion(int n) {
        int l = 1, r = n, m = 0;
        while(l < r) {
            m = l + (r-l)/2;
            if(isBadVersion(m))
                r = m;
            else
                l = m+1;
        }
        return l;
    }
}
```