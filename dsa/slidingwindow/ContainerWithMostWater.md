# Container With Most Water

You are given an integer array height of length n.
There are n vertical lines drawn such that the two endpoints of the ith line are (i, 0) and (i, height[i]).
Find two lines that together with the x-axis form a container, such that the container contains the most water.
Return the maximum amount of water a container can store.

https://leetcode.com/problems/container-with-most-water/

## Solution

- Start with two pointers at the ends
- Compute area
- Move the pointer pointing to the shorter line, because moving the taller one cannot increase area
- Repeat until pointers meet
- Track max area

Time O(n)
Space O(1)

```java
public int maxArea(int[] height) {   
    int maxArea = 0;
    int i = 0;
    int j = height.length - 1;
    while (i < j) {
        int h1 = height[i];
        int h2 = height[j];
        int width = j - i;
        int area = Math.min(h2, h1) * width;
        maxArea = Math.max(maxArea, area);
        if (h2 < h1) {
            j--;
        } else {
            i++;
        }
    }
    return maxArea;
}
```
