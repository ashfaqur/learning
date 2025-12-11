# Binary Tree Traversals

## In order traversal
```java
public void inOrderTraversal(TreeNode node) {
    if (node == null) {
        return;
    }
    inOrderTraversal(node.getLeft());
    System.out.println(node.getValue());
    inOrderTraversal(node.getRight());
}
```

        1
       / \
      2   3
     / \  /
    4  5 6
    
    4 2 5 1 6 3

Purpose:

Visits nodes in sorted order for BSTs.

Useful for printing elements in ascending order or evaluating expressions.

## Pre order traversal
```java
public void preOrderTraversal(TreeNode node) {
    if (node == null) {
        return;
    }
    System.out.println(node.getValue());
    preOrderTraversal(node.getLeft());
    preOrderTraversal(node.getRight());
}
```
        1
       / \
      2   3
     / \  /
    4  5 6

    1 2 4 5 3 6

Purpose:

Visits root before children.

Useful for copying trees, generating prefix expressions, or saving tree structure.

## Post order traversal
```java
public void postOrderTraversal(TreeNode node) {
    if (node == null) {
        return;
    }
    postOrderTraversal(node.getLeft());
    postOrderTraversal(node.getRight());
    System.out.println(node.getValue());
}
```

        1
       / \
      2   3
     / \  /
    4  5 6

    4 5 2 6 3 1

Purpose:

Visits children before root.

Useful for deleting a tree, evaluating postfix expressions, or freeing resources in bottom-up order.
