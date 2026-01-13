# Kth Largest Element in an Array


Given an integer array nums and an integer k, return the kth largest element in the array.

Note that it is the kth largest element in the sorted order, not the kth distinct element.

Can you solve it without sorting?

https://leetcode.com/problems/kth-largest-element-in-an-array/description/

## Solution with Sort

```java
public int findKthLargest(int[] nums, int k) {
    Arrays.sort(nums);
    return nums[nums.length - k];
}
```

