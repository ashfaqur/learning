# Linked List

```java
public class Node {
    Node nextNode;
    int data;
}
```
Appending a node

```java

private Node headNode;

public void appendData(int data){
    Node newNode = new Node(data);
    if (this.headNode == null){
        this.headNode = newNode;
        return;
    }
    Node lastNode = this.headNode;

    while(lastNode.getNextNode() != null){
        lastNode = lastNode.getNextNode();
    }
    lastNode.setNextNode(newNode);
}
```
Deleting a node

``` java
public void deleteNodeAt(int position) {
    if (this.headNode == null) {
        return;
    }
    if (position == 0) {
        this.headNode = this.headNode.getNextNode();
        return;
    }
    Node node = this.headNode;

    int counter = 0;
    while (node.getNextNode() != null) {
        counter++;
        Node current = node.getNextNode();
        if (counter == position) {
            node.setNextNode(current.getNextNode());
            return;
        }
        node = current;
    }
}
```

## Table of Contents:
- [Palindrome Linked List](PalindromeLinkedList.md)
