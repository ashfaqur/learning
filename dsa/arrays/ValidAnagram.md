# Valid Anagram

Given two strings s and t, return true if t is an anagram of s, and false otherwise.

An anagram is a word, phrase, or name formed by rearranging the letters of another, such as spar, formed from rasp.

https://leetcode.com/problems/valid-anagram

## Solution with Map (Ledger)

- Count characters from s in a map
- subtract counts with t
- if all counts are zero, it’s an anagram.

Time O(n)
Space O(n)

```java
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) {
            return false;
        }
        Map<Character, Integer> bucket = new HashMap<>();
        for (int i = 0; i < s.length(); i++) {
            bucket.put(s.charAt(i), bucket.getOrDefault(s.charAt(i), 0) + 1);
        }
        for (int i = 0; i < t.length(); i++) {
            if (!bucket.containsKey(t.charAt(i))) {
                return false;
            }
            bucket.put(t.charAt(i), bucket.get(t.charAt(i)) - 1);
        }
        for (Integer value : bucket.values()) {
            if (value != 0) {
                return false;
            }
        }
        return true;
    }
```

## Solution with Sort

- Sort both strings
- Compare character by character
- If all match, it’s an anagram

Time O(n log n) -> sort
Space O(n)

```java
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) {
            return false;
        }
        char[] sArray = s.toCharArray();
        char[] tArray = t.toCharArray();
        Arrays.sort(sArray);
        Arrays.sort(tArray);
        for (int i = 0; i < s.length(); i++) {
            if (sArray[i] != tArray[i]) {
                return false;
            }
        }
        return true;
    }
```

## Solution with Array

- For lowercase letters
- use a 26-element array: +1 for s, -1 for t
- all zeros at the end = anagram.

```java
public boolean isAnagram(String s, String t) {
    if (s.length() != t.length()){
        return false;
    } 
    int[] count = new int[26];
    
    for (int i = 0; i < s.length(); i++) {
        count[s.charAt(i) - 'a']++;
        count[t.charAt(i) - 'a']--;
    }

    // check if all counts are zero
    for (int c : count) {
        if (c != 0) return false;
    }

    return true;
}
```
