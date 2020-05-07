# 146. LRU Cache

Design and implement a data structure for **Least Recently Used (LRU) cache**. 
It should support the following operations: get and put in **O(1) time**.

`get(key)` - Get the value (will always be positive) of the key if the key exists in the cache, 
otherwise return -1.
`put(key, value)` - Set or insert the value if the key is not already present. When the cache 
reached its capacity, it should invalidate the least recently used item before inserting a new item.

The cache is *initialized* with a **positive capacity**.

- Example 1:
> LRUCache cache = new LRUCache( 2 /* capacity */ ); <br>
> cache.put(1, 1); <br>
> cache.put(2, 2); <br>
> cache.get(1);       // returns 1 <br>
> cache.put(3, 3);    // evicts key 2 <br>
> cache.get(2);       // returns -1 (not found) <br>
> cache.put(4, 4);    // evicts key 1 <br>
> cache.get(1);       // returns -1 (not found) <br>
> cache.get(3);       // returns 3 <br>
> cache.get(4);       // returns 4

## Optimal
Use a *map* and a *double linked list*. The map stores pairs `<int, DoubleListNode>`.
**Double List Node** stores the key, value, next and prev nodes. 
- When there's a *Get* operation, check if list is empty. If not empty, get the node from the map,
remove the node from the list and insert at the head. Finally, return node.value.
- When there's a *Put* operation, check if the node key exists in the map.
  - If it exists, update the node value, remove the node from the list and insert at the head.
  - If it doesn't exist, add a new node to the map and to the head of the list. Then check if 
    list.size() == capacity, if so, remove last node from the list and from the map.
    *Note*: the node has to have the key value, so when it get's removed from the list, we know
    the key to remove from the map.

**Time: O(1) <br> Space: O(n)**

*LRU Cache is not complex, but the DoubleLinkList is tricky and error-prone because all of the 
pointers that need to be updated*

### Go solution
```go
type LRUCache struct {
    cache map[int]*DoubleListNode
    list DoubleLinkedList
    capacity int
}

func Constructor(capacity int) LRUCache {
    cache := make(map[int]*DoubleListNode)
    list := NewDoubleLinkedList()
    lruCache := &LRUCache{cache: cache, capacity: capacity, list: list}
    return *lruCache
}


func (this *LRUCache) Get(key int) int {
    if node, found := this.cache[key]; found {
        this.list.RemoveNode(node)
        this.list.AddFirst(node)
        return node.val
    }
    return -1
}


func (this *LRUCache) Put(key int, value int)  {
    if node, found := this.cache[key]; found {
        node.val = value
        this.list.RemoveNode(node)
        this.list.AddFirst(node)
    } else {
        newNode := &DoubleListNode{value, key, nil, nil}
        this.cache[key] = newNode
        this.list.AddFirst(newNode)
        
        if this.list.Size() > this.capacity {
            tail := this.list.RemoveLast()
            delete(this.cache, tail.key)
        }
    }
}

// Below is the DLL Interface and Implementation

type DoubleListNode struct {
	val  int
	key  int
	next *DoubleListNode
	prev *DoubleListNode
}

type DoubleLinkedList interface {
	AddFirst(node *DoubleListNode)
	AddLast(node *DoubleListNode)
	RemoveFirst() *DoubleListNode
	RemoveLast() *DoubleListNode
	RemoveNode(node *DoubleListNode) bool
	IsEmpty() bool
	Size() int
}

type DoubleLinkedListImpl struct {
	head *DoubleListNode
	tail *DoubleListNode
	size int
}

func NewDoubleLinkedList() DoubleLinkedList {
	var list DoubleLinkedList
	list = &DoubleLinkedListImpl{}

	return list
}

func (d *DoubleLinkedListImpl) Size() int {
	return d.size
}

func (d *DoubleLinkedListImpl) IsEmpty() bool {
	if d.size == 0 {
		return true
	}
	return false
}

func (d *DoubleLinkedListImpl) AddFirst(node *DoubleListNode) {
	if d.IsEmpty() {
		d.head = node
		d.tail = node
	} else {
		node.prev = nil
		node.next = d.head
		d.head.prev = node
		d.head = node
	}
	d.size = d.size + 1
}

func (d *DoubleLinkedListImpl) AddLast(node *DoubleListNode) {
	if d.IsEmpty() {
		d.head = node
		d.tail = node
	} else {
		node.next = nil
		node.prev = d.tail
		d.tail.next = node
		d.tail = node
	}
	d.size = d.size + 1
}

func (d *DoubleLinkedListImpl) RemoveFirst() *DoubleListNode {
	if d.IsEmpty() {
		return nil
	}
	d.size = d.size - 1
	temp := d.head
	d.head = temp.next

	if d.head != nil {
		d.head.prev = nil
	} else {
		d.tail = nil
	}

	return temp
}

func (d *DoubleLinkedListImpl) RemoveLast() *DoubleListNode {
	if d.IsEmpty() {
		return nil
	}
	d.size = d.size - 1
	temp := d.tail
	d.tail = temp.prev

	if d.tail != nil {
		d.tail.next = nil
	} else {
		d.head = nil
	}

	return temp
}

func (d *DoubleLinkedListImpl) RemoveNode(node *DoubleListNode) bool {
	if d.IsEmpty() {
		return false
	}
	if node == d.head {
		d.RemoveFirst()
		return true
	}
	if node == d.tail {
		d.RemoveLast()
		return true
	}

	d.size = d.size - 1
	node.prev.next = node.next
	node.next.prev = node.prev

	return true
}
```
### Java solution
```java

```