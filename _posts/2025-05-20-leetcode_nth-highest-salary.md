---
layout: single
title: "Nth Highest Salary(LeetCode)"
categories: CodingTest
tags: [SQL]
---

## <a href="https://leetcode.com/problems/nth-highest-salary/" target="_blank">Nth Highest Salary</a>

```sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
    set N = N -1;
  RETURN (
      # Write your MySQL query statement below.
    SELECT DISTINCT salary
    FROM Employee
    ORDER BY salary DESC
    LIMIT 1 OFFSET N
  );
END
```

### 설명

- Employee 테이블을 사용하여 N번째로 높은 급여를 구하는 문제
- Create Function, Set, Limit, Offset의 사용법을 익힐 수 있는 문제
