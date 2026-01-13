# Trees

- [Trees](#trees)
  - [Normal Tree](#normal-tree)
  - [Binary Tree](#binary-tree)
  - [Binary Search Tree](#binary-search-tree)
- [Balanced vs Unbalanced Binary Trees](#balanced-vs-unbalanced-binary-trees)
  - [Balanced Tree](#balanced-tree)
  - [Unbalanced Tree](#unbalanced-tree)
- [Binary Tree Types](#binary-tree-types)
  - [Complete Binary Tree](#complete-binary-tree)
  - [Full Binary Tree](#full-binary-tree)
  - [Perfect Binary Tree](#perfect-binary-tree)
- [Binary Tree Traversals](#binary-tree-traversals)
  - [In order traversal](#in-order-traversal)
  - [Pre order traversal](#pre-order-traversal)
  - [Post order traversal](#post-order-traversal)

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


# Balanced vs Unbalanced Binary Trees

## Balanced Tree
- Left and right subtrees have similar height
- Height ≈ O(log n)
- Operations are efficient:
  - Search / Insert / Delete → O(log n)
  - Examples: AVL, Red-Black Tree, B-Tree

## Unbalanced Tree
- Nodes skew heavily to one side (like a linked list)
- Height can become O(n)
- Operations degrade to:
  - Search / Insert / Delete → O(n)

Intuition:

Balance prevents the tree from getting “tall and skinny.”

# Binary Tree Types

## Complete Binary Tree

All levels are fully filled except possibly the last, which is filled from left to right.

        1
       / \
      2   3
     / \  /
    4  5 6


## Full Binary Tree

Every node has either 0 or 2 children.

        1
       / \
      2   3
     / \   
    4  5   


## Perfect Binary Tree

A full binary tree where all levels are completely filled.

        1
       / \
      2   3
     / \  / \
    4  5 6  7



# Binary Tree Traversals

## In order traversal

- Visits nodes in sorted order for BSTs
- Useful for printing elements in ascending order or evaluating expressions.
  
```java
public void inOrderTraversal(TreeNode node) {
    if (node == null) {
        return;
    }
    inOrderTraversal(node.left);
    System.out.println(node);
    inOrderTraversal(node.right);
}
```

        1
       / \
      2   3
     / \  /
    4  5 6
    
    4 2 5 1 6 3

## Pre order traversal

- Visits root before children.
- Useful for copying trees, generating prefix expressions, or saving tree structure.
  
```java
public void preOrderTraversal(TreeNode node) {
    if (node == null) {
        return;
    }
    System.out.println(node);
    inOrderTraversal(node.left);
    inOrderTraversal(node.right);
}
```
        1
       / \
      2   3
     / \  /
    4  5 6

    1 2 4 5 3 6

## Post order traversal

- Visits children before root.
- Useful for deleting a tree, evaluating postfix expressions, or freeing resources in bottom-up order.

```java
public void postOrderTraversal(TreeNode node) {
    if (node == null) {
        return;
    }
    inOrderTraversal(node.left);
    inOrderTraversal(node.right);
    System.out.println(node);
}
```

        1
       / \
      2   3
     / \  /
    4  5 6

    4 5 2 6 3 1

