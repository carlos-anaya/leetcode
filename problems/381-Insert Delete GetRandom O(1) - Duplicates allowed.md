# 381. Insert Delete GetRandom O(1) - Duplicates allowed (Hard)

Design a data structure that supports all following operations in average *O(1)* time.

**Note: Duplicate elements are allowed.**

- `insert(val)`: Inserts an item val to the collection.
- `remove(val)`: Removes an item val from the collection if present.
- `getRandom()`: Returns a random element from current collection of elements. The probability of
each element being returned is linearly related to the number of same value the collection contains.

## Optimal
This solution expands on [380-Insert Delete GetRandom O(1)](Delete%20GetRandom%20O(1).md), but this
time we're suporting duplicates. Instead of a *map<int, int>* this time we use a 
`map<int, set<int>>`, so we can keep track of the duplicate positions for a given number. Same as in
the *non-duplicates* solution we keep track of all the numbers in a `list` so the *get random*
function is evenly distributed. For the *remove* operation, similar as before, we swap the last 
number to *any* position of the number to be removed and update both sets (element to to be removed
and swapped).

**Time: O(n)** (n -> number of operations; all operations are O(1)) <br> **Space: O(n)**

### Java solution
```java
class RandomizedCollection {
    Map<Integer,Set<Integer>> map;
    List<Integer> list;
    
    /** Initialize your data structure here. */
    public RandomizedCollection() {
        map = new HashMap();
        list = new ArrayList();
    }
    
    /** Inserts a value to the collection. Returns true if the collection did not already contain the specified element. */
    public boolean insert(int val) {
        list.add(val);
        if(map.containsKey(val)) {
            map.get(val).add(list.size() - 1);
            return false;
        }
        Set<Integer> set = new HashSet();
        set.add(list.size() - 1);
        map.put(val, set);
        return true;
    }
    
    /** Removes a value from the collection. Returns true if the collection contained the specified element. */
    public boolean remove(int val) {
        if(!map.containsKey(val)) {
            return false;
        }
        int lastIdx = list.size() - 1;
        int lastNum = list.get(lastIdx);

        // get any position of the item that will be removed
        Set<Integer> set = map.get(val);
        int idx = set.iterator().next();
        set.remove(idx);
        
        // swap the last element to the idx of the num to be removed
        list.set(idx, lastNum);
        list.remove(list.size() - 1);
        
        // update the idx of the swapped number
        map.get(lastNum).add(idx);
        map.get(lastNum).remove(lastIdx);
        
        // remove the entry from the map if it doesn't have any indices
        if(set.size() == 0) {
            map.remove(val);
        }
        
        return true;
    }
    
    /** Get a random element from the collection. */
    public int getRandom() {
        int rnd = new Random().nextInt(list.size());
        return list.get(rnd);
    }
}
```

### Go solution
```go
type RandomizedCollection struct {
    numMap map[int]*NumPair
    nums []int
    count int
}

/** Initialize your data structure here. */
func Constructor() RandomizedCollection {
    numMap := make(map[int]*NumPair)
    nums := make([]int, 0, 20)
    return RandomizedCollection{numMap, nums, 0}
}


/** Inserts a value to the collection. Returns true if the collection did not already contain the specified element. */
func (this *RandomizedCollection) Insert(val int) bool {
    this.nums = append(this.nums, val)
    if _, found := this.numMap[val]; !found {
        this.numMap[val] = NewNumPair(this.count)
        this.count++
        return true
    }
    this.numMap[val].add(this.count)
    this.count++
    return false
}


/** Removes a value from the collection. Returns true if the collection contained the specified element. */
func (this *RandomizedCollection) Remove(val int) bool {
    if _, found := this.numMap[val]; !found {
        return false
    }
    numPair := this.numMap[val]
    idxToRemove := numPair.get()
    idxLast := this.count - 1
    lastNum := this.nums[idxLast]
    // swap last with the idx of the element to remove
    this.nums[idxToRemove] = this.nums[idxLast]
    numPair.remove(idxToRemove)
    this.numMap[lastNum].remove(idxLast)
    this.numMap[lastNum].add(idxToRemove)
    if numPair.count == 0 {
        delete(this.numMap, val)
    }
    this.count--
    this.nums = this.nums[:this.count]
    return true
}


/** Get a random element from the collection. */
func (this *RandomizedCollection) GetRandom() int {
    if this.count == 0 { return -1 }
    if this.count == 1 { return this.nums[0] }
    return this.nums[rand.Intn(this.count)]
}

type NumPair struct {
    count int
    idxs map[int]bool
}

func NewNumPair(idx int) *NumPair {
    idxs := make(map[int]bool)
    idxs[idx] = true
    return &NumPair{1, idxs}
}

func (n *NumPair) get() int {
    for k, _ := range n.idxs {
        return k
    }
    return -1
}

func (n *NumPair) add(idx int) {
    n.idxs[idx] = true
    n.count++
}

func (n *NumPair) remove(idx int) {
    n.count--
    delete(n.idxs, idx)
}
```
