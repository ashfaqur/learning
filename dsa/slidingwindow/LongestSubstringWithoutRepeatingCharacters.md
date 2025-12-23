# Longest Substring Without Repeating Characters

Given a string s, find the length of the longest substring without duplicate characters.

https://leetcode.com/problems/longest-substring-without-repeating-characters/description/


## Solution:

Use a sliding window with two pointers (left and right) and a HashSet to track unique characters. Expand the window by moving right, and shrink from left only when a duplicate appears. Each character is added and removed at most once → O(n) time, O(n) space.


Time O(n)
Space O(n)

```java
    public int lengthOfLongestSubstring(String s) {
        if (s == null || s.isEmpty()) {
            return 0;
        }
        Set<Character> window = new HashSet<>();
        int max = 0;
        int left = 0;
        for (int right = 0; right < s.length(); right++) {
            while (window.contains(s.charAt(right))) {
                window.remove(s.charAt(left));
                left++;
            }
            window.add(s.charAt(right));
            if (window.size() > max) {
                max = window.size();
            }

        }
        return max;
    }
```

## Non optimal 

Time O(n2)
Space O(n)

```java
    public int lengthOfLongestSubstring2(String s) {
        char[] chars = s.toCharArray();
        int max = 0;
        for (int i = 0; i < chars.length; i++) {
            int count = 1;
            Set<Character> set = new HashSet<>();
            set.add(chars[i]);
            for (int j = i + 1; j < chars.length; j++) {
                if (set.contains(chars[j])) {
                    break;
                }
                set.add(chars[j]);
                count++;
            }
            if (count > max) {
                max = count;
            }
        }
        return max;
    }
```
