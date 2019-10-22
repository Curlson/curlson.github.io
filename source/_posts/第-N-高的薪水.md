---
layout: ppost
title: 第 N 高的薪水
date: 2019-10-22 20:00:00
tags:
---

编写一个 SQL 查询，获取 Employee 表中第 n 高的薪水（Salary）。

+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
例如上述 Employee 表，n = 2 时，应返回第二高的薪水 200。如果不存在第 n 高的薪水，那么查询应返回 null。

+------------------------+
| getNthHighestSalary(2) |
+------------------------+
| 200                    |
+------------------------+

<!--- more --->

格式：

```mysql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
    RETURN (
        # Write your MySQL query statement below.
	    # 在下面编写 SQL 查询语句
       

    );
END
```

* `DECLARE` 变量声明
* `SET` 变量赋值
* `IF THEN`, `ELSE`, `END IF` 流程控制

##### 思路一、

与求第二高的类似：

```mysql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT -- 这里是 returns 第三人称
BEGIN
	# 这里就是解决了直接写 limit n-1，1 报错的问题
	SET N = N -1;
	RETURN (
    	SELECT DISTINCT salary FROM employee ORDER BY salary DESC LIMIT N, 1
    ); -- ； 别忘了 
END
```

##### 思路二、

相比思路一，这里考虑 `N`不合理的情况

```mysql
## 第一版
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT -- 这里是 returns 第三人称
BEGIN
    DECLARE P1 INT;
    DECLARE P2 INT;
    IF(N < 1) THEN
        SET P1 = 0, P2 = 0;
    ELSE
        SET P1 = N - 1, P2 = 1;
    END IF;

	# 这里就是解决了直接写 limit n-1，1 报错的问题
	SET N = N -1;
	RETURN (
    	SELECT IFNULL(
            (
                SELECT DISTINCT salary FROM employee ORDER BY salary DESC LIMIT P1, P2
            )

        , NULL)
    ); -- ； 别忘了 
END

### 精简版
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
	DECLARE P1 DEFAULT N-1;
	IF(P1 < 0) THEN
		RETURN NULL
	ELSE RETURN (
    	SELECT IFNULL((
        	SELECT 
            DISTINCT Salary 
            FROM Employee 
            ORDER BY Salary 
            DESC LIMIT P1, 1
        ), NULL)
    )
    END IF;
END
```





[第 N 高的薪水](https://leetcode-cn.com/problems/nth-highest-salary)