---
title: 力扣579 员工累计薪水
date: 2025-12-02
tags: [sql, leetcode, 窗口函数, sum, range, rows]
keywords: [LeetCode579, 累计薪水, RANGE, ROWS, 窗口函数]
---
#sql学习 
知识点：窗口函数 range 和 rows 的区别
日期：2025.12.02
#力扣579
题目：查询员工累计薪水（最近3个月）
思路：1.题目要求计算每个员工最近3个月（含当月）的累计工资，且不包括最近的一个月（即只算倒数第二个月及以前的数据）。
2.难点在于**月份可能有缺失**（例如有1月、2月、4月，缺了3月）。
3.如果使用 `ROWS`，会错误地把物理上的前一行（2月）和前前一行（1月）加给4月，导致统计范围变成（1,2,4）。
4.必须使用 ==RANGE BETWEEN 2 PRECEDING AND CURRENT ROW==，它是基于**数值**的。对于4月，它只找 `[4-2, 4]` 即 `[2, 4]` 范围内的月份，会自动忽略1月。

示例数据：
```
id | month | salary
-- | ----- | ------
1  | 1     | 100
1  | 2     | 200
1  | 4     | 400  <-- 缺了3月
```
如果用 ROWS：4月的结果 = 400(4月) + 200(2月) + 100(1月) = 700 (❌ 错误，包含了一月)
如果用 RANGE：4月的结果 = 400(4月) + 200(2月) = 600 (✅ 正确，只算2-4月)

函数解释：
- ==ROWS BETWEEN ...==：基于**物理行号**。不管你排序列的值是多少，它只认“我上面那一行”。
- ==RANGE BETWEEN ...==：基于**排序列的数值**。它会做数学减法。
  - 必须配合 ==ORDER BY== 使用。`RANGE` 的范围是根据 `ORDER BY` 的字段值计算的。
  - 例如 `ORDER BY month RANGE BETWEEN 2 PRECEDING` 意味着范围是 `[month - 2, month]`。
  - 如果 `ORDER BY` 是日期，则可以使用 `INTERVAL`，例如 `RANGE BETWEEN INTERVAL 7 DAY PRECEDING`。
- **注意**：
  - 如果 `ORDER BY` 包含多个字段，`RANGE` 会报错（因为无法定义多维距离），只能用 `ROWS`。
  - 如果没有 `ORDER BY`，`ROWS` 和 `RANGE` 都不能用。

sql代码为：
```sql
SELECT 
    id, 
    month, 
    Salary
FROM (
    SELECT 
        id, 
        month, 
        -- 使用 RANGE 确保只统计数学意义上的"最近3个月"
        SUM(salary) OVER (
            PARTITION BY id 
            ORDER BY month 
            RANGE BETWEEN 2 PRECEDING AND CURRENT ROW
        ) AS Salary,
        -- 倒序排名，排第一的就是最近的一个月
        RANK() OVER (PARTITION BY id ORDER BY month DESC) as rnk
    FROM Employee
) t
WHERE rnk > 1  -- 排除最近的一个月
ORDER BY id ASC, month DESC;
```
