# Remove Nth Node From End of List

Given the head of a linked list, remove the nth node from the end of the list and return its head.

## Solution

- Use a dummy node so removing the head becomes a normal case.
- Move fast ahead n steps, then move slow + fast together until fast reaches the end.
- Now slow.next is the node to delete — unlink it and return dummy.next.

```java
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(0, head);
        ListNode slow = dummy;
        ListNode fast = dummy;
        for (int i = 0; i < n; i++) {
            fast = fast.next;
        }
        while (fast.next != null) {
            slow = slow.next;
            fast = fast.next;
        }
        slow.next = slow.next.next;
        return dummy.next;
    }
```
