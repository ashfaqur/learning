# Sliding Window Maximum

You are given an array of integers nums, there is a sliding window of size k which is moving from the very left of the array to the very right. You can only see the k numbers in the window. Each time the sliding window moves right by one position.

Return the max sliding window.

Example 1:

Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]

https://leetcode.com/problems/sliding-window-maximum/description/

# Solution With Deque

Front = max, remove expired from front, remove smaller from back, add new index at back

Time O(n) - One loop

Space O(k) - holding items in queue

```java
    public int[] maxSlidingWindow(int[] nums, int k) {
        int[] results = new int[nums.length - k + 1];
        int n = nums.length;
        Deque<Integer> deque = new ArrayDeque<>();
        for (int i = 0; i < nums.length; i++) {
            if (!deque.isEmpty() && deque.peekFirst() <= (i - k)) {
                deque.pollFirst();
            }
            while (!deque.isEmpty() && nums[i] >= nums[deque.peekLast()]) {
                deque.pollLast();
            }
            deque.offerLast(i);
            if (i >= k - 1) {
                results[i - k + 1] = nums[deque.peekFirst()];
            }
        }
        return results;
    }
```

# Solution with Two Pointers

Time O(n * k) - two loops

Space O(1) - no extra space (holding results do not count)

Slide the window, scan each element inside, pick the max.

```java
    public int[] maxSlidingWindow(int[] nums, int k) {
        int[] maxWindow = new int[nums.length - k + 1];
        int left = 0;
        while (left <= nums.length - k) {
            int maxValue = Integer.MIN_VALUE;
            for (int i = left; i < left + k; i++) {
                if (nums[i] > maxValue) {
                    maxValue = nums[i];
                }
            }
            maxWindow[left] = maxValue;
            left++;
        }
        return maxWindow;
    }
```
