# Move Zeros

Given an integer array nums, move all 0's to the end of it while maintaining the relative order of the non-zero elements.

Note that you must do this in-place without making a copy of the array.

Example 1:

Input: nums = [0,1,0,3,12]
Output: [1,3,12,0,0]

Example 2:

Input: nums = [0]
Output: [0]

https://leetcode.com/problems/move-zeroes/description/

## Solution

Two pointer approach. One  to track position where the next non-zero goes another to scan the array. If the element is non-zero, place it at that pointer and increment the pointer. Zeros naturally end up at the end.

Time O(n)
Space O(1)

```java
    public void moveZeroes(int[] nums) {

        int position = 0;

        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != 0) {
                if (i != position) {
                    //swap
                    int tmp = nums[i];
                    nums[i] = nums[position];
                    nums[position] = tmp;
                }
                position++;
            }

        }
    }
```
