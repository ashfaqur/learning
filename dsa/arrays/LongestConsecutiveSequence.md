# Longest Consecutive Sequence

Given an unsorted array of integers nums, return the length of the longest consecutive elements sequence.

You must write an algorithm that runs in O(n) time.

Example 1:

Input: nums = [100,4,200,1,3,2]
Output: 4
Explanation: The longest consecutive elements sequence is [1, 2, 3, 4]. Therefore its length is 4.

Example 2:

Input: nums = [0,3,7,2,5,8,4,6,0,1]
Output: 9

Example 3:

Input: nums = [1,0,1,2]
Output: 3


https://leetcode.com/problems/longest-consecutive-sequence/description/

## Solution with Sort

Sort → Skip duplicates → Count streaks → Reset on gaps → Track max

Time O (n log n) -> because of sort 
Space O (log n) -> because of sort recursion

```java
public int longestConsecutive(int[] nums) {
    if (nums.length == 0) {
        return 0;
    }
    Arrays.sort(nums);
    int longest = 0;
    int streak = 1;
    for (int i = 1; i < nums.length; i++) {
        // duplicate
        if (nums[i] == nums[i - 1]) {
            continue;
        }
        if ((nums[i] - 1) == nums[i - 1]) {
            streak++;
        } else {
            // reset streak
            streak = 1;
        }
        if (streak > longest) {
            longest = streak;
        }

    }
    return longest;
}

```

## Solution with Map

Put all numbers in a set → only start counting if num-1 is missing → walk forward → keep max

```java
public int longestConsecutive(int[] nums) {
    Set<Integer> set = new HashSet<>();
    for (int num : nums) {
        set.add(num);
    }
    int longest = 0;

    for (int num : set) {
        // only start from sequence head
        if (!set.contains(num - 1)) {
            int streak = 1;
            int next = num + 1;
            while (set.contains(next)) {
                streak++;
                next++;
            }
            if (streak > longest) {
                longest = streak;
            }
        }
    }
    return longest;
}

```
