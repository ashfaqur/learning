# Two Sum

Given an array of integers nums and an integer target,
return indices of the two numbers such that they add up to target.

## Brute Force



```java
public int[] twoSumBrute(int[] nums, int target) {
    for (int i = 0; i < nums.length; i++) {
        for (int j = i + 1; j < nums.length; j++) {
            if ((nums[i] + nums[j]) == target) {
                return new int[]{i, j};
            }
        }
    }
    return new int[]{};
}
```
Time O(n2)
Space O(1)

## Optimized

Store each number’s index in a hash map and, for every new number,
check instantly whether its complement (target - num) is already in the map.

```java
public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        int need = target - nums[i];
        if (map.containsKey(need)) {
            return new int[]{map.get(need), i};
        }
        map.put(nums[i], i);
    }
    return new int[]{};
}
```

Time O(n)
Space O(n)
