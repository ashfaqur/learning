# Trees


## Normal Tree

```java
public class TreeNode {
    int value;
    TreeNode[] children;
}
```


## Binary Tree

Each tree node has at most 2 child nodes

```java
public class TreeNode {
    int value;
    TreeNode left;
    TreeNode right;
}
```
Example

        8
       / \
      3   10
     / \    \
    6   1    14
       /      \
      7        4


## Binary Search Tree

A binary tree where each node’s left child contains smaller values and the right child contains larger values.

        8
       / \
      3   10
     / \    \
    1   6    14
       / \   /
      4   7 13


## Table of Contents:
- [Binary Tree Types](BinaryTreeTypes.md)
- [Traversals](BinaryTreeTraversals.md)
- [Max Depth](MaxDepth.md)
