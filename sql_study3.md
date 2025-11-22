---
title: 力扣1398 买A买B不买C
date: 2025-11-22
tags: [sql, leetcode, 聚合, having, exists, join]
keywords: [LeetCode1398, 买A买B不买C, HAVING, EXISTS]
---
#sql学习
知识点：判断存在思路
日期：2025.11.22
#力扣1398
题目：判断客户买A买B但没买过C的
思路：1.错误思路，把 A/B/C 标记成不同数字再求和，容易因重复购买导致去重复杂
2.正确思路：使用 ==true/false== 计数（如 `SUM(product_name='A')>0`）或使用 ==WHERE EXISTS== 判断存在

sql代码为：
```
select c.customer_id,
       c.customer_name
from (
  select customer_id
  from Orders
  group by customer_id
  having sum(product_name = 'A') > 0
     and sum(product_name = 'B') > 0
     and sum(product_name = 'C') = 0
) o
join Customers c on o.customer_id = c.customer_id
order by c.customer_id;
```