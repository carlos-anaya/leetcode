# 415. Add Strings (Easy)

Given two non-negative integers `num1` and `num2` represented as string, return the *sum* of both
numbers.

**Note**:
- The length of both `num1` and `num2` is < 5100.
- Both `num1` and `num2` contains only digits 0-9 and they don't contain any leading zero.
- You must not use any built-in BigInteger library or convert the inputs to integer directly.

- Example 1:
> **Input**: num1="99", num2="1" <br>
> **Output**: "100"

## Optimal
Loop over both strings, starting from the right-most char. Take char by char and get the *int digit*
and add them. Take the *mod* and append it to a string builder, and if the num is greater than/equal
to *10* we have a *carry*.

At the end, check if there is a *carry* and reverse the string.

**Time: O(max(n1, n2))** (n1, n2 -> lengths of num1, num2) <br> **Space: O(max(n1, n2))**

### Java solution
```java
class Solution {
    public String addStrings(String num1, String num2) {
        int n1 = num1.length() - 1;
        int n2 = num2.length() - 1;
        int carry = 0;
        
        StringBuilder sb = new StringBuilder();
        while(n1 >= 0 || n2 >= 0) {
            // default the digits to 0, then update if num1/num2 have chars remaining
            int d1 = 0, d2 = 0;
            if(n1 >= 0) {
                d1 = num1.charAt(n1) - '0'; 
                n1--;
            }
            if(n2 >= 0) {
                d2 = num2.charAt(n2) - '0';
                n2--;
            }
            int sum = d1 + d2 + carry;
            carry = sum / 10;
            sb.append(sum % 10);
        }
        if(carry==1)
            sb.append(1);
        
        return sb.reverse().toString();
    }
}
```