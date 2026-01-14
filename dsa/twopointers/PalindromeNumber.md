# Palindrome Number

A palindrome is a number or word that reads the same forward and backward.
Given an integer x, return true if x is a Palindrome, and false otherwise.

## Solution

Reverse number with remainder and division by 10

```java
public boolean isPalindromeFullReverse(int x) {
    if (x < 0) {
        return false;
    }
    int tempX = x;
    int reverse = 0;

    while (tempX > 0) {
        int digit = tempX % 10;
        reverse = reverse * 10 + digit;
        tempX = tempX / 10;
    }

    return reverse == x;
}
```

Reverse half the number and then compare with reverse for even and with division by 10 for odd digits.

```java
public boolean isPalindromeHalfReverse(int x) {
    if (x < 0 || (x != 0 && (x % 10 == 0))) {
        return false;
    }
    int reverse = 0;
    while (x > reverse) {
        int digit = x % 10;
        reverse = reverse * 10 + digit;
        x = x / 10;
    }
    return (x == reverse) || (x == reverse / 10);
}
```
