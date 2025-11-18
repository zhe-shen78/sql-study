---
title: 力扣176 第二高的薪水
date: 2025-11-18
tags: [sql, leetcode, 子查询, limit, offset]
keywords: [LeetCode176, SecondHighestSalary, 子查询, LIMIT, OFFSET]
---
#sql
知识点：select特性输出null（子查询）
日期：2025.11.18
#力扣176
题目：查询第二高的薪水（仅输出一个）
思路：1.使用order by从大到小排序，使用参数desc倒序
	   2.使用==limit 1==输出一个，利用==offset 1==可以从前往后跳行
	   3.利用select子查询特性，**当内部查询返回空时会自动转成null**
sql代码为：
```
SELECT (
    SELECT DISTINCT salary
    FROM Employee
    ORDER BY salary DESC
    LIMIT 1 OFFSET 1
) AS SecondHighestSalary;
```