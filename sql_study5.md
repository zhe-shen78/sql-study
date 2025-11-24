---
title: 力扣1285 寻找连续区间
date: 2025-11-24
tags: [sql, leetcode, 窗口函数, row_number, 连续区间, 聚合]
keywords: [LeetCode1285, 连续登录, 等差分组, row_number]
---
#sql学习 
知识点：数学差值法
日期：2025.11.24
#力扣1285
题目：寻找连续区间（连续登录，连续数字）
思路：对给定表进行==row_number==唯一排序，利用等差数列特性，`值-序号`恒定可分组
sql代码为：
```
select min(log_id) as start_id,
       max(log_id) as end_id
from (
  select log_id,
         log_id - row_number() over (order by log_id) as grp
  from Logs
) t
group by grp
order by start_id;
```
