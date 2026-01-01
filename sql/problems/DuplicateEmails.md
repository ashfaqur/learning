# Duplicate Emails

Write a solution to report all the duplicate emails. Note that it's guaranteed that the email field is not NULL.

https://leetcode.com/problems/duplicate-emails/description/

## Solution

```sql
SELECT p.email AS Email
FROM person p
WHERE p.email IS NOT NULL
GROUP BY p.email
HAVING COUNT(p.email) > 1 
```

```sql
SELECT email AS Email
FROM person
GROUP BY email
HAVING email IS NOT NULL AND COUNT(email) > 1;
```
