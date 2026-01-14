# Valid Parentheses

Given a string s containing just the characters '(', ')', '{', '}', '[' and ']',
determine if the input string is valid.

## Solution

Use a stack to match openings with closings; stack must be empty at the end.

```java
    public boolean isValid(String s) {
        Map<Character, Character> map = Map.of(')', '(', ']', '[', '}', '{');
        Deque<Character> stack = new ArrayDeque<>();
        for (int i = 0; i < s.length(); i++) {
            char character = s.charAt(i);
            if (map.containsKey(character)) {
                if (stack.isEmpty()) {
                    return false;
                }
                char expected = map.get(character);
                if (expected != stack.pop()) {
                    return false;
                }
            } else if (map.containsValue(character)) {
                stack.push(character);
            }
        }
        return stack.isEmpty();
    }
```
