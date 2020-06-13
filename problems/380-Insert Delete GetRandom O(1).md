# 380. Insert Delete GetRandom O(1) (Medium)

Design a data structure that supports all following operations in average **O(1) time**.

- `insert(val)`: Inserts an item val to the set if not already present.
- `remove(val)`: Removes an item val from the set if present.
- `getRandom()`: Returns a random element from current set of elements. Each element must have the 
  **same probability** of being returned.

## Optimal
We need to keep track of the numbers inserted and removed but we don't care about the *order* of the
elements, so we can use a `list` and a `map`. The map keeps track of the *numbers* and its
*positions* and the list keeps track of all the elements, so we can generate a *random* number from
*0 to n - 1* and return that element from the list. The only concern is the *delete* operation since
we can't delete from a list in O(1) time unless it is the last element of the list. Then, we can 
"swap" the element to be removed with the last element, so we can safely remove the last element in
O(1) time. 

**Time: O(n)** (n -> number of operations; all operations are O(1)) <br> **Space: O(n)**

### Java solution
```java
class RandomizedSet {
    Map<Integer,Integer> map;
    List<Integer> list;
    
    /** Initialize your data structure here. */
    public RandomizedSet() {
        map = new HashMap();
        list = new ArrayList();
    }
    
    /** Inserts a value to the set. Returns true if the set did not already contain val. */
    public boolean insert(int val) {
        if(map.containsKey(val))
            return false;
        
        map.put(val, list.size());
        list.add(val);
        return true;
    }
    
    /** Removes a value from the set. Returns true if the set contained the specified element. */
    public boolean remove(int val) {
        if(!map.containsKey(val))
            return false;
        
        int lastNum = list.get(list.size() - 1);
        int toRemoveIdx = map.get(val);
        
        // swap "val" with the last number of the list
        map.put(lastNum, toRemoveIdx);
        list.set(toRemoveIdx, lastNum);
        
        // remove "val" from the list and map
        list.remove(list.size() - 1);
        map.remove(val);
        
        return true;
    }
    
    /** Get a random element from the set. */
    public int getRandom() {
        int r = new Random().nextInt(list.size());
        return list.get(r);
    }
}
```