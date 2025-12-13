#  Valid Palindrome

A phrase is a palindrome if, after converting all uppercase letters into lowercase letters and removing all non-alphanumeric characters, it reads the same forward and backward. Alphanumeric characters include letters and numbers.

Given a string s, return true if it is a palindrome, or false otherwise.

The trick is two pointers at the ends, skip non applicable, compare values and meet in the middle, then true otherwise false.

```java
    public boolean isPalindrome(String s) {
        int left = 0;
        int right = s.length() - 1;

        while (left <= right) {
            char leftValue = s.charAt(left);
            if (!Character.isLetterOrDigit(leftValue)) {
                left++;
                continue;
            }
            char rightvalue = s.charAt(right);
            if (!Character.isLetterOrDigit(rightvalue)) {
                right--;
                continue;
            }
            if (Character.toLowerCase(leftValue) != Character.toLowerCase(rightvalue)) {
                return false;
            }
            left++;
            right--;
        }
        return true;
    }
```
