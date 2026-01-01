# Palindrome Linked List

Given the head of a singly linked list, return true if it is a Palindrome or false otherwise.

https://leetcode.com/problems/palindrome-linked-list/description/


## Solution

```java

    public boolean isPalindrome(ListNode head) {

        if (head == null) {
            return false;
        }

        if (head.next == null) {
            return true;
        }

        ListNode slow = head;
        ListNode fast = head;

        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }

        // split list
        ListNode first = head;
        while (first.next != slow) {
            first = first.next;
        }
        first.next = null;
        // move slow to second half in case of odd list
        if (fast != null) {
            slow = slow.next;
        }

        // reverse list
        ListNode prev = null;
        ListNode current = slow;
        ListNode next = null;
        while (current != null) {
            next = current.next;
            current.next = prev;
            prev = current;
            current = next;
        }

        first = head;
        ListNode second = prev;

        while (first != null && second != null) {
            if (first.val != second.val) {
                return false;
            }
            first = first.next;
            second = second.next;
        }
        return true;
    }
```
