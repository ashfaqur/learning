# Binary Search

Given an array of integers nums which is sorted in ascending order, and an integer target, write a function to search target in nums. If target exists, then return its index. Otherwise, return -1.

You must write an algorithm with O(log n) runtime complexity.

## Solution:

Keep searching while the range is inclusive (low <= high).

Check the middle.

If target is smaller → move high left.

If target is larger → move low right.

```java
public int search(int[] nums, int target) {
    int low = 0;
    int high = nums.length - 1;

    while (low <= high) {
        int mid = low + (high - low) / 2;
        int midValue = nums[mid];

        if (midValue == target) {
            return mid;
        }

        if (target < midValue) {
            high = mid - 1;
        } else {
            low = mid + 1;
        }
    }
    return -1;

}
```
