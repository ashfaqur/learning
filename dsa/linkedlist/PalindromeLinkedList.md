# Palindrome Linked List

Given the head of a singly linked list, return true if it is a Palindrome or false otherwise.

https://leetcode.com/problems/palindrome-linked-list/description/


## Solution

1️⃣ Runner technique
slow finds the middle, fast detects odd/even length.

2️⃣ Skip middle if odd
If fast != null, move slow one step (ignore center element).

3️⃣ Reverse 2nd half
Reverse starting from slow so both halves move forward symmetrically.

4️⃣ Compare halves

head → …      vs      reversed second half

Stop on first mismatch.

👉 If everything matches → palindrome.

```java

    public boolean isPalindrome(ListNode head) {
        if (head == null) {
            return false;
        }
        // single node is a palindrome
        if (head.next == null) {
            return true;
        }
        // runner to find middle
        ListNode slow = head;
        ListNode fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        // find the node before slow pointer to split list
        ListNode first = head;
        while (first.next != slow) {
            first = first.next;
        }
        first.next = null;
        // move slow to second half in case of odd list where fast pointer is not null
        if (fast != null) {
            slow = slow.next;
        }
        // reverse second list
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
