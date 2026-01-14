# Rotate Array

Given an integer array nums, rotate the array to the right by k steps, where k is non-negative.

Example 1:
Input: nums = [1,2,3,4,5,6,7], k = 3
Output: [5,6,7,1,2,3,4]
 
https://leetcode.com/problems/rotate-array/

## Solution in place with reverse

- Reverse the full array.
- Right rotation: reverse first k, the reverse rest.
- Left rotation: with k = n − k, reverse first k the reverse rest.

Time O(n)
Space O(1)

```java

    public void rotateRight(int[] nums, int k) {
        int n = nums.length;
        k = k % n;
        if (k == 0) {
            return;
        }
        reverseArray(nums, 0, n - 1);
        reverseArray(nums, 0, k - 1);
        reverseArray(nums, k, n - 1);
    }

    public void rotateLeft(int[] nums, int k) {
        int n = nums.length;
        k = k % n;
        if (k == 0) {
            return;
        }
        k = n - k;
        reverseArray(nums, 0, n - 1);
        reverseArray(nums, 0, k - 1);
        reverseArray(nums, k, n - 1);
    }


    public void reverseArray(int[] nums, int start, int end) {
        while (start < end) {
            int temp = nums[start];
            nums[start] = nums[end];
            nums[end] = temp;
            start++;
            end--;
        }
    }
```


## Solution with additonal array

Time O(n)
Space O(n)

```java
    public void rotate2(int[] nums, int k) {
        int n = nums.length;
        k = k % n;
        if (k == 0) {
            return;
        }

        int[] newNums = new int[n];
        int index = 0;

        // last k elements
        for (int i = n - k; i < n; i++) {
            newNums[index++] = nums[i];
        }

        // first n - k elements
        for (int i = 0; i < n - k; i++) {
            newNums[index++] = nums[i];
        }

        // copy back
        for (int i = 0; i < n; i++) {
            nums[i] = newNums[i];
        }
    }
```
