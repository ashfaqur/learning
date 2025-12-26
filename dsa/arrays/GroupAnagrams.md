# Group Anagrams

Given an array of strings strs, group the Anagrams together. You can return the answer in any order.

https://leetcode.com/problems/group-anagrams/description/

## Solution with Sort and Map:

Sort string → map key → add to list → map.values() gives grouped anagrams."

```java
public List<List<String>> groupAnagrams(String[] strs) {
    Map<String, List<String>> map = new HashMap<>();
    for (String str : strs) {
        char[] chars = str.toCharArray();
        Arrays.sort(chars);
        String key = new String(chars);
        map.putIfAbsent(key, new ArrayList<>());
        map.get(key).add(str);
    }
    return new ArrayList<>(map.values());
}
```
