# Merge Sorted Array

## Solution In Place

Time: O(m + n)

Space: O(n)

Merge from the back to avoid overwriting.
Use three pointers (a, b, c) starting at the ends.
Place the larger element at c, move pointers left.
Only copy leftovers from nums2 — nums1 is already in place.

```java
public void merge(int[] nums1, int m, int[] nums2, int n) {
    int a = m - 1;
    int b = n - 1;
    int c = m + n - 1;

    while (a >= 0 && b >= 0) {
        if (nums2[b] > nums1[a]) {
            nums1[c] = nums2[b];
            b--;
        } else {
            nums1[c] = nums1[a];
            a--;
        }
        c--;
    }

    while (b >= 0) {
        nums1[c] = nums2[b];
        b--;
        c--;
    }
}
```


## Solution Extra Array

Time: O(m + n)

Space: O(n)

Three pointers, forward merge.
Compare arrayA[a] and arrayB[b], put the smaller into merged[m].
Move the pointer you used.
When one array ends, copy the rest.

Using for loop


```java
    public int[] mergeSortedArray(int[] arrayA, int[] arrayB) {
        int[] mergedArray = new int[arrayA.length + arrayB.length];
        int a = 0;
        int b = 0;
        int m = 0;

        for (m = 0; m < mergedArray.length; m++) {
            if (a < arrayA.length && b < arrayB.length) {
                if (arrayA[a] < arrayB[b]) {
                    mergedArray[m] = arrayA[a];
                    a++;
                } else {
                    mergedArray[m] = arrayB[b];
                    b++;
                }
            } else if (a < arrayA.length) {
                mergedArray[m] = arrayA[a];
                a++;
            } else if (b < arrayB.length) {
                mergedArray[m] = arrayB[b];
                b++;
            }
        }
        return mergedArray;
    }
```

Using while loop

```java
    public int[] merge(int[] arrayA, int[] arrayB) {
        int[] mergedArray = new int[arrayA.length + arrayB.length];
        int a = 0;
        int b = 0;
        int m = 0;

        while (a < arrayA.length && b < arrayB.length) {
            if (arrayA[a] < arrayB[b]) {
                mergedArray[m] = arrayA[a];
                a++;
            } else {
                mergedArray[m] = arrayB[b];
                b++;
            }
            m++;
        }

        while (a < arrayA.length) {
            mergedArray[m] = arrayA[a];
            a++;
            m++;
        }

        while (b < arrayB.length) {
            mergedArray[m] = arrayB[b];
            b++;
            m++;
        }
        return mergedArray;
    }
```
