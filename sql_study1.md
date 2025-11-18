---
title: 力扣185 部门工资前三
date: 2025-11-12
tags: [sql, leetcode, 窗口函数, dense_rank, rank, row_number, 子查询, join]
keywords: [LeetCode185, 部门工资前三, partition by, dense_rank]
---
#sql学习
知识点：窗口函数，子查询
日期：2025.11.12
#力扣185
题目 查询不同部门工资前三的员工
思路
1.先把不同部门分开，使用窗口函数中==partition by==可以分组并输出所有符合条件的行，接着使用order by可以给工资排序，默认从小到大，参数desc可以倒序
2.使用窗口函数==**dense_rank**==，可以给每一行标序号，遇到重复的会重复标记，不会跳号，例如 1 2 2 3。具体作用是找出工资排名前三的命名为pai，这样重复标号可以把工资相同的算进去
   需要区别==row_number==，这个是给唯一编号，从上到下1234...
   还需区别==rank==,这个会跳号，比如 1 2 2 4。这三个函数功能都是标记序号
3.将Employee与Department连接起来，使用join
4.现在基本完成，子查询上述部分，方便使用where来筛选pai<=3，这样就找出不同部门排名前三的工资对应的姓名了

sql代码为：
```
select Department, Employee, Salary
from (
  select d.name as Department,
         e.name as Employee,
         e.salary as Salary,
         dense_rank() over (partition by e.departmentId order by e.salary desc) as pai
  from Employee e
  join Department d on e.departmentId = d.id
) a
where pai <= 3;
```


