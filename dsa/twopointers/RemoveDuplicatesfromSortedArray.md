# Remove Duplicates from Sorted Array

https://leetcode.com/problems/remove-duplicates-from-sorted-array/description/


## Solution Using Pointer

position tracks the length of the unique prefix.

Only move position when you find a new value (i.e., when nums[i] != nums[i-1]).
Write the value at nums[position], then increment.
At the end, position itself = number of unique elements

Time O(n)
Space O(1)

```java
    public int removeDuplicates(int[] nums) {
        if (nums.length == 0) {
            return 0;
        }
        int position = 1;
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] != nums[i - 1]) {
                nums[position] = nums[i];
                position++;
            }
        }
        return position;
    }
```





## Solution Using Set

Time O(n)
Space O(k)

```java
public int removeDuplicates(int[] nums) {
    Set<Integer> set = new HashSet<>();
    int position = 0;
    for (int i = 0; i < nums.length; i++) {
        if (!set.contains(nums[i])) {
            nums[position] = nums[i];
            set.add(nums[i]);
            position++;
        }
    }
    return set.size();
}
```



