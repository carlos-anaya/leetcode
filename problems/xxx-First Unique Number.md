# xxx. First Unique Number

You have a queue of integers, you need to retrieve the first unique integer in the queue.

Implement the `FirstUnique` class with the following methods:

- **FirstUnique(int[] nums)** Initializes the object with the numbers in the queue.
- **int showFirstUnique()** returns the value of the first unique integer of the queue, and returns
  -1 if there is no such integer.
- **void add(int value)** insert value to the queue.

- Example 1:
> **Input**: ["FirstUnique","showFirstUnique","add","showFirstUnique","add","showFirstUnique",
> "add","showFirstUnique"] <br>
> [[[2,3,5]],[],[5],[],[2],[],[3],[]] <br>
> **Output**: [null,2,null,2,null,3,null,-1] <br>
> **Explanation**:<br>
> FirstUnique firstUnique = new FirstUnique([2,3,5]); <br>
> firstUnique.showFirstUnique(); // return 2 <br>
> firstUnique.add(5);            // the queue is now [2,3,5,5] <br>
> firstUnique.showFirstUnique(); // return 2 <br>
> firstUnique.add(2);            // the queue is now [2,3,5,5,2] <br>
> firstUnique.showFirstUnique(); // return 3 <br>
> firstUnique.add(3);            // the queue is now [2,3,5,5,2,3] <br>
> firstUnique.showFirstUnique(); // return -1
- Example 2:
> **Input**: ["FirstUnique","showFirstUnique","add","showFirstUnique"] <br>
[[[809]],[],[809],[]] <br>
> **Output**: [null,809,null,-1] <br>
> **Explanation**:<br>
> FirstUnique firstUnique = new FirstUnique([809]); <br>
> firstUnique.showFirstUnique(); // return 809 <br>
> firstUnique.add(809);          // the queue is now [809,809] <br>
> firstUnique.showFirstUnique(); // return -1

## Optimal
Use a `queue` and a `hashmap` with *<char, count>* pairs. 
- **Add**: Insert the character to the queue and increment its count in the map.
- **ShowFirstUnique**: Pop elements from the queue until an element has *count == 1* (in the map).

**Time: O(1) for Add, O(1) for Show (amortized) <br> Space: O(n)**

### Go solution
```go
type FirstUnique struct {
    numMap map[int]int
    queue Queue
}

func Constructor(nums []int) FirstUnique {
    m := make(map[int]int)
    q := NewQueue(len(nums))
    fu := FirstUnique{m, q}
    
    for _, num := range nums {
        fu.Add(num)
    }
    return fu
}

func (this *FirstUnique) ShowFirstUnique() int {
    // Remove from queue elements that are not unique
    for !this.queue.Empty() {
        if count, found := this.numMap[this.queue.First()]; found && count == 1 {
            break
        }
        this.queue.Remove()
    }
    // First() returns -1 if queue is empty
    return this.queue.First()
}


func (this *FirstUnique) Add(value int)  {
    if _, found := this.numMap[num]; !found {
        this.numMap[num] = 0
    }
    this.numMap[num]++
    this.queue.Add(num)
}

type Queue struct {
    nums []int
    size int
}

func NewQueue(capacity int) Queue {
    nums := make([]int, 0, capacity*2)
    q := Queue{nums, 0}
    return q
}

func (q *Queue) Empty() bool {
    return q.size == 0
}

func (q *Queue) Add(num int) {
    q.nums = append(q.nums, num)
    q.size++
}

func (q *Queue) First() int {
    if q.Empty() {
        return -1
    }
    return q.nums[0]
}

func (q *Queue) Remove() {
    if !q.Empty() {
        q.nums = q.nums[1:]
        q.size--
    }
}
```