---
title: 第二高的薪水
date: 2019-10-19 10:01:27
tags: 
 - Mysql
 - SQL架构
 - Leetcode
categories:
- [SQL架构, Mysql]
---

编写一个 SQL 查询，获取 Employee 表中第二高的薪水（Salary） 。
<!-- more -->


| Id | Salary |
|----|----|
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
例如上述 Employee 表，SQL查询应该返回 200 作为第二高的薪水。如果不存在第二高的薪水，那么查询应返回 null。


| SecondHighestSalary |
|----|
| 200 |




##### 方法一：

1. `DISTINCT` 去除重复
2. `OFFSET` 为偏移量
3. `DESC` 倒序
4. 本题结合：`ORDER`, `LIMIT`, `OFFSET`, `DISTINCT` 可以做到筛选出第二高的值
5. 可以通过临时表，或者使用 `IFNLL` 函数，输出默认值

```mysql
# select distinct salary from Employee order by salary desc limit 1,1 就可以，
# 但是输不出null，所以外面再加一层 
select (
    select distinct salary from Employee order by salary desc limit 1,1
) as SecondHighestSalary ;
```

##### 方法二：

```mysql
select max(Salary) as SecondHighestSalary 
from Employee 
where salary<>(
 	select max(Salary)  from Employee 
)
```

##### 方法三：

```mysql
# 说 group by 比 distinct 快一点
SELECT 
IFNULL(
    (SELECT Salary
    	From Employee
    	GROUP BY Salary
   	 	ORDER BY Salary DESC
    	LIMIT 1 OFFSET 1
    ),NULL
)
AS SecondHighestSalary
```

来源：力扣（LeetCode）
[176. 第二高的薪水](https://leetcode-cn.com/problems/second-highest-salary)