# Search in Rotated Sorted Array

There is an integer array nums sorted in ascending order (with distinct values).

Given the array nums after the possible rotation and an integer target, return the index of target if it is in nums, or -1 if it is not in nums.

You must write an algorithm with O(log n) runtime complexity.

https://leetcode.com/problems/search-in-rotated-sorted-array/description/

## Solution:

One side of the array is always sorted.
Find which side is sorted, check if the target lies inside it —
search that side, otherwise search the other.

```java
public int search(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;
        int midValue = nums[mid];
        if (midValue == target) {
            return mid;
        }
        if (nums[left] <= midValue) {
            if (nums[left] <= target && target < midValue) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        } else {
            if (nums[mid] < target && target <= nums[right]) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
    }
    return -1;
}
```
