---
title: MySQL训练
date: 2021-03-16 09:03:46
tags:
 - mysql
---

## 前言

​	身为一个后端开发员，sql 语句的编写应该是很熟练的，但是由于缺少实际项目，也没有地方训练，本人的 sql 语句能力一直处于很一般的情况。

​	最近发现力扣上面居然还有 sql 的题目，所以打算全部认真做一遍。由于是 sql 语句，所以一般只有答案。

<!-- more -->

## [175. 组合两个表](https://leetcode-cn.com/problems/combine-two-tables/)

难度简单776收藏分享切换为英文接收动态反馈

SQL架构

表1: `Person`

```
+-------------+---------+
| 列名         | 类型     |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
PersonId 是上表主键
```

表2: `Address`

```
+-------------+---------+
| 列名         | 类型    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
AddressId 是上表主键
```

编写一个 SQL 查询，满足条件：无论 person 是否有地址信息，都需要基于上述两表提供 person 的以下信息：

```
FirstName, LastName, City, State
```

### 答案

```mysql
SELECT FirstName, LastName, City, State
FROM Person LEFT OUTER JOIN Address
ON Person.PersonId = Address.PersonId
```

由于题目要求 person 无论是否有地址都要输出，所以应该使用外连接，这里使用左外连接（保存左边所有数据）

## [176. 第二高的薪水](https://leetcode-cn.com/problems/second-highest-salary/)

难度简单742收藏分享切换为英文接收动态反馈

SQL架构

编写一个 SQL 查询，获取 `Employee` 表中第二高的薪水（Salary） 。

```
+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
```

例如上述 `Employee` 表，SQL查询应该返回 `200` 作为第二高的薪水。如果不存在第二高的薪水，那么查询应返回 `null`。

```
+---------------------+
| SecondHighestSalary |
+---------------------+
| 200                 |
+---------------------+
```

### 答案

```sql
SELECT (
	SELECT DISTINCT Salary 
	FROM Employee
	ORDER BY Salary DESC
	LIMIT 1,1
	) AS SecondHighestSalary;
```

```mysql
SELECT IFNULL((
	SELECT DISTINCT Salary 
	FROM Employee
	ORDER BY Salary DESC
	LIMIT 1,1
),null) AS SecondHighestSalary;
```

由于题目要求不存在第二高的薪水需要返回 null，所以在外面再套一个 SELECT，也可以使用 IFNULL(a , b)这个函数，如果 a 为空，则返回 b，第二种更形象一点。

## [177. 第N高的薪水](https://leetcode-cn.com/problems/nth-highest-salary/)

编写一个 SQL 查询，获取 `Employee` 表中第 *n* 高的薪水（Salary）。

```
+----+--------+
| Id | Salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
```

例如上述 `Employee` 表，*n = 2* 时，应返回第二高的薪水 `200`。如果不存在第 *n* 高的薪水，那么查询应返回 `null`。

```
+------------------------+
| getNthHighestSalary(2) |
+------------------------+
| 200                    |
+------------------------+
```

### 答案

```mysql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  # 将 N 的值减 1，因为 LIMIT后面无法接表达式
  SET N := N - 1;
  RETURN (
      # Write your MySQL query statement below.
      SELECT IFNULL((
            SELECT DISTINCT Salary 
            FROM Employee
            ORDER BY Salary DESC
            LIMIT N,1
        ),null) AS SecondHighestSalary
  );
END
```

​		和上面一题一样的方法，就是将2换成了变量 n，记得先处理一下 N ，LIMIT 后面不能接表达式。

## [178. 分数排名](https://leetcode-cn.com/problems/rank-scores/)

难度中等704收藏分享切换为英文接收动态反馈

SQL架构

编写一个 SQL 查询来实现分数排名。

如果两个分数相同，则两个分数排名（Rank）相同。请注意，平分后的下一个名次应该是下一个连续的整数值。换句话说，名次之间不应该有“间隔”。

```
+----+-------+
| Id | Score |
+----+-------+
| 1  | 3.50  |
| 2  | 3.65  |
| 3  | 4.00  |
| 4  | 3.85  |
| 5  | 4.00  |
| 6  | 3.65  |
+----+-------+
```

例如，根据上述给定的 `Scores` 表，你的查询应该返回（按分数从高到低排列）：

```
+-------+------+
| Score | Rank |
+-------+------+
| 4.00  | 1    |
| 4.00  | 1    |
| 3.85  | 2    |
| 3.65  | 3    |
| 3.65  | 3    |
| 3.50  | 4    |
+-------+------+
```

**重要提示：**对于 MySQL 解决方案，如果要转义用作列名的保留字，可以在关键字之前和之后使用撇号。例如 **`Rank`**

### 答案

​	自己的话，将分数按分数递降排序，然后计算每一次分数大于他的人，不过执行效率比较低。

​	看题解看到一个比较快的，使用 mysql 的内置函数 `DENSE_RANK()`，这个函数需要搭配 OVER 使用，OVER里面表明排序规则。`DENSE_RANK()` 是并列连续排名。

​	个人答案

```mysql
# Write your MySQL query statement below
SELECT a.Score AS Score,(
	SELECT count(DISTINCT b.Score)
	FROM Scores AS b
	WHERE b.Score >= a.Score
) AS `Rank`
FROM Scores AS a
ORDER BY Score DESC
```

​	其他答案

```mysql
select score, DENSE_RANK() OVER (ORDER BY Score DESC) as 'Rank'
from Scores;
```

## [180. 连续出现的数字](https://leetcode-cn.com/problems/consecutive-numbers/)

难度中等382收藏分享切换为英文接收动态反馈

SQL架构

表：`Logs`

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| num         | varchar |
+-------------+---------+
id 是这个表的主键。
```

编写一个 SQL 查询，查找所有至少连续出现三次的数字。

返回的结果表中的数据可以按 **任意顺序** 排列。

查询结果格式如下面的例子所示：

```
Logs 表：
+----+-----+
| Id | Num |
+----+-----+
| 1  | 1   |
| 2  | 1   |
| 3  | 1   |
| 4  | 2   |
| 5  | 1   |
| 6  | 2   |
| 7  | 2   |
+----+-----+

Result 表：
+-----------------+
| ConsecutiveNums |
+-----------------+
| 1               |
+-----------------+
1 是唯一连续出现至少三次的数字。
```

### 答案

​	如果主键是连续的情况下，可以使用三个自身表进行查询，id 相差 1，并且值相同，出现两次，也就是下面这个

```mysql
SELECT DISTINCT
    l1.Num AS ConsecutiveNums
FROM
    Logs l1,
    Logs l2,
    Logs l3
WHERE
    l1.Id = l2.Id - 1
    AND l2.Id = l3.Id - 1
    AND l1.Num = l2.Num
    AND l2.Num = l3.Num
;

```

​	但是这是建立在主键相同的情况下，否则你就得自己建一个额外的列，然后再来查询。

​	还有一种就是使用 Lead 函数，将数据下拉一行，两行，然后再来比较

```mysql
SELECT DISTINCT num AS ConsecutiveNums
FROM (SELECT num,lead(num,1) over() AS num1,lead(num,2) over() AS num2
	  FROM Logs) AS data
WHERE data.num = data.num1 AND data.num = data.num2;
```

