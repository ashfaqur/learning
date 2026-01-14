# Reverse Linked List

Given the head of a singly linked list, reverse the list, and return the reversed list.

https://leetcode.com/problems/reverse-linked-list/

## Solution

- Keep three pointer prev, current, next
- At each step, point current.next to prev and move all pointers forward.

```java
public ListNode reverseList(ListNode head) {
    if (head == null){
        return null;
    }
    ListNode prev = null;
    ListNode current = head;
    ListNode next = head.next;

    while (current != null) {
        next = current.next;
        current.next = prev;
        prev = current;
        current = next;
    }

    return prev;
}
```

