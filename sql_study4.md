---
title: 力扣1407 Top Travellers 与 607 Sales Person
date: 2025-11-23
tags: [sql, leetcode, coalesce, exists, not exists, join, 聚合]
keywords: [LeetCode1407, TopTravellers, COALESCE, LeetCode607, SalesPerson, NOT EXISTS, RED]
---
#sql学习 
知识点：coalesce多条件null处理，exists与select 1的搭配
日期：2025.11.23
#力扣1407
题目：查询排名靠前的人
要点：使用==coalesce==（value1，value2）会返回第一个不为null的数字，可在保留用户行的同时把缺失里程转为0
sql代码为:
```
select u.name,
       coalesce(sum(r.distance), 0) as travelled_distance
from Users u
left join Rides r on u.id = r.user_id
group by u.id, u.name
order by travelled_distance desc, u.name;
```
#力扣607
题目：查询与RED无关的人
思路：使用==NOT EXISTS==（exists会排除空值）排除与公司RED有关的订单；子查询用`select 1`与内连接即可。
sql代码为：
```
select s.name
from SalesPerson s
where not exists (
  select 1
  from Orders o
  join Company c on c.com_id = o.com_id
  where c.name = 'RED'
    and o.sales_id = s.sales_id
);
```
