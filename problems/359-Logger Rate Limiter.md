# 359. Logger Rate Limiter (Easy)

Design a logger system that receive stream of messages along with its timestamps, each message
should be printed if and only if it is not printed in the **last 10 seconds**.

Given a message and a timestamp (in seconds granularity), return true if the message should be
printed in the given timestamp, otherwise returns false.

It is possible that several messages arrive roughly at the same time.

- Example:
> Logger logger = new Logger(); <br><br>
> // logging string "foo" at timestamp 1 <br>
> logger.shouldPrintMessage(1, "foo"); returns true; <br><br>
> // logging string "bar" at timestamp 2 <br>
> logger.shouldPrintMessage(2,"bar"); returns true; <br><br>
> // logging string "foo" at timestamp 3 <br>
> logger.shouldPrintMessage(3,"foo"); returns false; <br><br>
> // logging string "bar" at timestamp 8 <br>
> logger.shouldPrintMessage(8,"bar"); returns false; <br><br>
> // logging string "foo" at timestamp 10 <br>
> logger.shouldPrintMessage(10,"foo"); returns false; <br><br>
> // logging string "foo" at timestamp 11 <br>
> logger.shouldPrintMessage(11,"foo"); returns true;

## Optimal
Use a `hashmap` to keep track of messages and their timestamp. In case of a new message or the
message timestamp is more than 10 seconds ago, return true and insert/update the message.

**Time: O(N)** (N -> Number of unique messages) <br> **Space: O(N)**

### Go solution
```go
type Logger struct {
    logger map[string]int
}


/** Initialize your data structure here. */
func Constructor() Logger {
    return Logger{make(map[string]int)}
}

/** 
  Returns true if the message should be printed in the given timestamp, otherwise returns false.
  If this method returns false, the message will not be printed.
  The timestamp is in seconds granularity.
*/
func (this *Logger) ShouldPrintMessage(timestamp int, message string) bool {
    // new value or last one was 10+ seconds ago
    if val, found := this.logger[message]; !found || val + 10 <= timestamp {
        this.logger[message] = timestamp
        return true
    }
    return false
}
```
