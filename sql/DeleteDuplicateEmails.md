# Delete Duplicate Emails

Write a solution to delete all duplicate emails, keeping only one unique email with the smallest id.

https://leetcode.com/problems/delete-duplicate-emails/description/

## Solution

```sql
DELETE
FROM person p1
USING person p2
WHERE p1.email = p2.email
  AND p1.id > p2.id;
```
