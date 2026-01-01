# Weave List

Given a singly-linked list, rearrange it so that nodes from the first and second halves are woven together in alternating order:

a1 → a2 → … → ak → b1 → b2 → … → bm


becomes

a1 → b1 → a2 → b2 → … 


The weave should:

preserve ordering within each half

work for both even and odd list lengths

run in O(n) time and O(1) extra space

If the list has an odd length, the middle element remains in the first half.

## Solution

Slow/Fast → find middle

slow ends at the end of first half (true middle for odd length)

Cut list into two halves

Weave by alternating pointers


```java
    public void weaveList() {
        if (this.head == null || this.head.next == null) {
            return;
        }
        // Find middle node with slow
        Node slow = head;
        Node fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        // Split lists
        Node first = this.head;
        Node second = slow.next;
        slow.next = null;

        // Weave
        while (first != null && second != null) {
            Node fNext = first.next;
            Node sNext = second.next;
            first.next = second;
            second.next = fNext;
            first = fNext;
            second = sNext;
        }
    }
```
