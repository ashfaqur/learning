# Employees Earning More Than Their Managers

https://leetcode.com/problems/employees-earning-more-than-their-managers/description/

## Solution Using JOIN

```sql
SELECT e.name AS Employee
FROM employee AS e
INNER JOIN employee AS m
ON e.managerId = m.id
WHERE e.salary > m.salary
;
```

## Solution With Subquery

```sql
SELECT e.name AS Employee
FROM employee AS e
WHERE e.salary > (
        SELECT m.salary
        FROM employee AS m
        WHERE m.id = e.managerId
    )
;
```
