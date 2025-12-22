# Three Sum

Given an integer array nums, return all the triplets [nums[i], nums[j], nums[k]] such that i != j, i != k, and j != k, and nums[i] + nums[j] + nums[k] == 0.

Notice that the solution set must not contain duplicate triplets.

## Solution With Brute

``` java
public List<List<Integer>> threeSumBrute(int[] nums) {
    List<List<Integer>> items = new ArrayList<>();
    for (int i = 0; i < nums.length; i++) {
        for (int j = i + 1; j < nums.length; j++) {
            for (int k = j + 1; k < nums.length; k++) {
                if (nums[i] + nums[j] + nums[k] == 0) {
                    List<Integer> triplet = Arrays.asList(nums[i], nums[j], nums[k]);
                    Collections.sort(triplet);
                    if (!items.contains(triplet)) {
                        items.add(triplet);
                    }
                }
            }
        }
    }
    return items;
}
```

## Solution with Two Sum Hashmap

```java
    public List<List<Integer>> threeSumWithTwoSum(int[] nums) {
        List<List<Integer>> items = new ArrayList<>();
        for (int i = 0; i < nums.length; i++) {
            int target = -nums[i];
            Map<Integer, Integer> map = new HashMap<>();
            for (int j = i + 1; j < nums.length; j++) {
                int need = target - nums[j];
                if (map.containsKey(need)) {
                    int index = map.get(need);
                    List<Integer> triplet = Arrays.asList(nums[i], nums[index], nums[j]);
                    Collections.sort(triplet);
                    if (!items.contains(triplet)) {
                        items.add(triplet);
                    }
                }
                map.put(nums[j], j);
            }
        }
        return items;
    }
```

### Solution with Sort then Pointers

Sort the array first → enables two-pointer logic and easy duplicate handling.

Fix one element (i), then use two pointers (left = i+1, right = end) to find two-sum = -nums[i].

Skip duplicates for i, left, and right to ensure unique triplets.

Move pointers properly: left++ if sum < target, right-- if sum > target, left++ & right-- after finding a valid triplet.


Time O(n2)
Space O(1)


```java
public List<List<Integer>> threeSum(int[] nums) {
    Arrays.sort(nums);
    List<List<Integer>> items = new ArrayList<>();
    for (int i = 0; i < nums.length; i++) {
        if (i != 0 && nums[i] == nums[i - 1]) {
            continue;
        }
        int target = -nums[i];
        int left = i + 1;
        int right = nums.length - 1;
        while (left < right) {
            if (nums[left] + nums[right] == target) {
                List<Integer> triplet = Arrays.asList(nums[i], nums[left], nums[right]);
                items.add(triplet);
                left++;
                right--;
                while (left < right && nums[left] == nums[left - 1]) {
                    left++;
                }
                while (left < right && nums[right] == nums[right + 1]) {
                    right--;
                }
            } else if (nums[left] + nums[right] < target) {
                left++;
            } else {
                right--;
            }
        }
    }
    return items;
}
```
