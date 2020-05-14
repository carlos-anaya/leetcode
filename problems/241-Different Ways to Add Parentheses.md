# 241. Different Ways to Add Parentheses (Medium)

Given a string of numbers and operators, return all possible results from computing all the 
different possible ways to group numbers and operators. The valid operators are `+`, `-` and `*`.

- Example 1:
> **Input**: "2-1-1" <br>
> **Output**: [0, 2] <br>
> **Explanation**: The expresions are <br>
> ((2-1)-1) = 0 <br>
> (2-(1-1)) = 2
- Example 2:
> **Input**: "2\*3-4\*5" <br>
> **Output**: [-34, -14, -10, -10, 10] <br>
> **Explanation**: The expressions are <br>
> (2\*(3-(4\*5))) = -34 <br>
> ((2\*3)-(4\*5)) = -14 <br>
> ((2\*(3-4))\*5) = -10 <br>
> (2\*((3-4)\*5)) = -10 <br>
> (((2\*3)-4)\*5) = 10

## Brute Force - Recursion
The easiest way to recurse is to loop over the string until we find an operand, then apply the same
recursion over the left and right substrings. Each will return a list of results, then we loop over
the results and apply the operand.

- E.g. "2-1\*3+4" becomes 
1. fn(2) - fn(1\*3+4)
2. fn(2-1) \* fn(3+4)
3. fn(2-1\*3) + fn(4)
4. the second operand of expr 1 has two results: fn(1) \* fn(3+4) and fn(1\*3) + fn(4)

```go
func diffWaysToCompute(input string) []int {
    ans := make([]int, 0, 10)
    for i, c := range input {
        if !isOperator(c) {
            continue
        }
        leftResults := diffWaysToCompute(input[:i])
        rightResults := diffWaysToCompute(input[i+1:])
        
        for _, num1 := range leftResults {
            for _, num2 := range rightResults {
                ans = append(ans, evaluate(num1, num2, c))
            }
        }
    }
    // the input has no operands, so it's a num
    if(len(ans) == 0) {
        num, _ := strconv.Atoi(input)
        ans = append(ans, num)
    }
    return ans
}

func isOperator(c rune) bool {
    return c == '+' || c == '-' || c == '*'
}

func evaluate(num1, num2 int, op rune) int {
    if op == '+' {
        return num1 + num2
    } else if op == '-' {
        return num1 - num2
    }
    return num1 * num2
}
```

**Time: O(n²) <br> Space: O(1)**

## Optimal - Memorization
There are multiple repeated expressions and we can save "re-evaluating" them by *memorizing*
previous expressions and results. We can use a *map<string, List<int>>* and each time we get the
*left/right results* we first check in the map if we have that expression already evaluated.

**Time: O(n) <br> Space: O(n)**

### Go solution
```go
func diffWaysToCompute(input string) []int {
    exprMap := make(map[string][]int)
    return diffWays(input, &exprMap)
}

func diffWays(input string, exprMap *map[string][]int) []int {
    ans := make([]int, 0, 10)
    
    for i, c := range input {
        if !isOperator(c) {
            continue
        }
        // check in map if there's a list of results for
        // left & right expressions
        leftResults := getResults(input[:i], exprMap)
        rightResults := getResults(input[i+1:], exprMap)
        
        for _, num1 := range leftResults {
            for _, num2 := range rightResults {
                ans = append(ans, evaluate(num1, num2, c))
            }
        }
    }
    // the input has no operands, so it's a single num
    if(len(ans) == 0) {
        num, _ := strconv.Atoi(input)
        ans = append(ans, num)
    }
    // append the result list to the map
    (*exprMap)[input] = ans
    return ans
}

func getResults(expr string, exprMap *map[string][]int) []int {
    if res, found := (*exprMap)[expr]; found {
        return res
    } 
    return diffWays(expr, exprMap)
}
```
### Java solution
```java
class Solution {
    public List<Integer> diffWaysToCompute(String input) {
        Map<String,List<Integer>> exprMap = new HashMap();
        
        return diffWays(input, exprMap);
    }
                        
    private List<Integer> diffWays(String input, Map<String,List<Integer>> exprMap) {
        List<Integer> res = new ArrayList();
        
        for(int i = 0; i < input.length(); i++) {
            char c = input.charAt(i);
            if(!isOperand(c)) {
                continue;
            }
            // check if map has values for left & right expressions
            List<Integer> resLeft = findValue(input.substring(0, i), exprMap);
            List<Integer> resRight = findValue(input.substring(i+1), exprMap);
            
            for(int num1 : resLeft) {
                for(int num2 : resRight) {
                    res.add(evaluate(num1, num2, c));
                }
            }
        }
        
        // no operands were found, so it's a single number
        if(res.size() == 0)
            res.add(Integer.parseInt(input));
        exprMap.put(input, res);
        
        return res;
    }
    
    private boolean isOperand(char c) {
        return c == '+' || c == '-' || c == '*';
    }
    
    private List<Integer> findValue(String input,  Map<String,List<Integer>> exprMap) {
        if(exprMap.containsKey(input))
            return exprMap.get(input);
        return diffWays(input, exprMap);
    }
    
    private int evaluate(int a, int b, char op) {
        if(op == '+')
            return a + b;
        if(op == '-')
            return a - b;
        return a * b;
    }
}
```