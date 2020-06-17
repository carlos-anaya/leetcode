# 67. Add Binary (Easy)

Given two binary strings, return their sum (also a binary string).

The input strings are both *non-empty* and contains only characters `1` or `0`.

- Example 1:
> **Input**: a = "11", b = "1" <br>
> **Output**: "100"
- Example 2:
> **Input**: a = "1010", b = "1011" <br>
> **Output**: "10101"

## Optimal
Loop over both strings, starting from the right-most char. Take char by char and get the *int digit*
and add them. Take the *mod* and append it to a string builder, and if the num is greater than/equal
to *2* we have a *carry*.

At the end, check if there is a *carry* and reverse the string.

**Time: O(max(n1, n2))** (n1, n2 -> lengths of num1, num2) <br> **Space: O(max(n1, n2))**

### Java solution
```java
class Solution {
    public String addBinary(String a, String b) {
        int lenA = a.length() - 1;
        int lenB = b.length() - 1;
        int carry = 0;
        StringBuilder sb = new StringBuilder();
        while(lenA >= 0 || lenB >= 0) {
            int d1 = 0, d2 = 0;
            if(lenA >= 0) {
                d1 = a.charAt(lenA) - '0';
                lenA--;
            }
            if(lenB >= 0) {
                d2 = b.charAt(lenB) - '0';
                lenB--;
            }
            int sum = d1 + d2 + carry;
            carry = sum / 2;
            sb.append(sum % 2);
        }
        if(carry == 1)
            sb.append(1);
        return sb.reverse().toString();
    }
}
```
