# LRU Cache

Least Recently Used Cache

https://leetcode.com/problems/lru-cache/

## Solution

- HashMap = find fast,
- Doubly Linked List = reorder fast.
- get(key) → move node to head (MRU)
- put(key)
- update + move to head if exists
- if full → evict tail (LRU), then insert at head
- Each node stores (key, value) so eviction can also remove it from the map
- Head = Most Recently Used
- Tail = Least Recently Used

Rule of thumb:
- Detach
- Move/Insert
- Re-link (O(1))
- never traverse.

```java

public class LRUCache {

    private static class Node {
        int key;
        int value;
        Node next;
        Node previous;

        public Node(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }

    final Map<Integer, Node> map = new HashMap<>();

    int capacity;
    Node head;
    Node tail;


    public LRUCache(int capacity) {
        this.capacity = capacity;
    }

    public int get(int key) {
        int value = -1;
        Node node = this.map.get(key);
        if (node != null) {
            value = node.value;
            moveNodeToFront(node);
        }
        return value;
    }

    public void put(int key, int value) {
        if (this.capacity <= 0) {
            return;
        }
        Node existingNode = this.map.get(key);
        if (existingNode != null) {
            existingNode.value = value;
            moveNodeToFront(existingNode);
        } else {
            if (this.map.size() >= this.capacity) {
                removeTail();
            }
            Node node = new Node(key, value);
            this.map.put(key, node);
            attachToHead(node);
        }
    }

    private void removeTail() {
        if (this.tail == null) {
            return;
        }
        this.map.remove(this.tail.key);
        if (this.tail.previous != null) {
            this.tail.previous.next = null;
            this.tail = this.tail.previous;
        } else {
            this.head = null;
            this.tail = null;
        }
    }

    private void attachToHead(Node node) {
        if (node == null) {
            return;
        }
        node.previous = null;
        node.next = this.head;

        if (this.head != null) {
            this.head.previous = node;
        }
        this.head = node;
        if (this.tail == null) {
            this.tail = node;
        }
    }

    private void moveNodeToFront(Node node) {
        if (node == null) {
            return;
        }
        if (node == this.head) {
            return;
        }
        Node previous = node.previous;
        Node next = node.next;
        if (previous != null) {
            previous.next = next;
        }
        if (next != null) {
            next.previous = previous;
        }
        if (node == tail) {
            this.tail = previous;
        }
        attachToHead(node);
    }
}

```
