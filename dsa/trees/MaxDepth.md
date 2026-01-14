# Maximum Depth of Binary Tree

Given the root of a binary tree, return its maximum depth.
A binary tree's maximum depth is the number of nodes along
the longest path from the root node down to the farthest leaf node.

## Solution

Use recursion to reach the leaves and compute depth on the way back up.

Time O(n) - nodes in tree
Space O(h) - h can be worst case n or log n in case of balanced tree

```java
public int maxDepth(TreeNode node) {
    if (node == null) {
        return 0;
    }
    return 1 + Math.max(maxDepth(node.left), maxDepth(node.right));
}
```


