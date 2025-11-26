---
title: 力扣1709 访问日期之间的最大空档期
date: 2025-11-26
tags: [sql, leetcode, 窗口函数, lead, lag, datediff]
keywords: [LeetCode1709, 最大空档期, LEAD, LAG, DATEDIFF]
---
#sql学习 
知识点：窗口函数lead(),lag()
日期：2025.11.26
#力扣1709
题目：访问日期之间的最大空档期
思路：1.题目提供 `UserVisits(user_id, visit_date)` 按用户分组；需要相邻访问的间隔
示例数据：
```
user_id | visit_date
------- | ----------
1       | 2020-10-20
1       | 2020-11-28
1       | 2020-12-03
2       | 2020-10-05
2       | 2020-12-09
3       | 2020-11-11
```
2.使用==LEAD(列, 移动步数, 默认值)==获取“下一次访问日期”，默认值设为今天 `2021-01-01`
3.用==DATEDIFF(后, 前)==计算天数差（MySQL返回后-前），取每个用户的最大值
函数解释：
- ==LEAD(选择列, 移动步长, 遇到缺失填什么)==：
  - 必填：选择列（当前行要对比的列）；
  - 可选：移动步长（默认 1，表示下一行）；
  - 可选：默认值（当“下一行”不存在时返回此值；不填则返回 NULL）。
  - 必须配合==OVER(partition by ... order by ...)==使用，`partition by` 指定分组，`order by` 指定组内顺序。
- ==LAG(选择列, 移动步长, 遇到缺失填什么)==：与 `LEAD` 相反，取“上一行”的值；语法与参数含义完全一致。
- ==DATEDIFF(后, 前)==：返回“后日期 - 前日期”的天数；本题为 `datediff(next_visit, visit_date)`。
sql代码为：
```
select user_id,
       max(datediff(next_visit, visit_date)) as biggest_window
from (
  select user_id,
         visit_date,
         lead(visit_date, 1, '2021-01-01') over (
           partition by user_id
           order by visit_date
         ) as next_visit
  from UserVisits
) t
group by user_id
order by user_id;
```
