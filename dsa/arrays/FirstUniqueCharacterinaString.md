# First Unique Character in a String

Given a string s, find the first non-repeating character in it and return its index.
If it does not exist, return -1.

https://leetcode.com/problems/first-unique-character-in-a-string/

## Solution

- Count frequencies
- Return first char with count = 1

Time O(n)
Space O(1)


```java
public int firstUniqChar(String s) {
    Map<Character, Integer> map = new HashMap<>();
    for (int i = 0; i < s.length(); i++) {
        Character item = s.charAt(i);
        map.put(item, map.getOrDefault(item, 0) + 1);
    }
    for (int i = 0; i < s.length(); i++) {
        if (map.get(s.charAt(i)) == 1) {
            return i;
        }
    }
    return -1;
}
```
