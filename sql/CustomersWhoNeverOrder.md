# Customers Who Never Order

Write a solution to find all customers who never order anything.

https://leetcode.com/problems/customers-who-never-order/description/

## Solution

```sql
SELECT c.name AS Customers
FROM customers c
LEFT JOIN orders o
ON c.id = o.customerId
WHERE o.id IS NULL
;
```
