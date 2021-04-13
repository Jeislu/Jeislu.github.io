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

2021/4/13

​	抱歉，一段时间没来联系，后面会保持练习的

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

## [181. 超过经理收入的员工](https://leetcode-cn.com/problems/employees-earning-more-than-their-managers/)

难度简单356收藏分享切换为英文接收动态反馈

SQL架构

`Employee` 表包含所有员工，他们的经理也属于员工。每个员工都有一个 Id，此外还有一列对应员工的经理的 Id。

```
+----+-------+--------+-----------+
| Id | Name  | Salary | ManagerId |
+----+-------+--------+-----------+
| 1  | Joe   | 70000  | 3         |
| 2  | Henry | 80000  | 4         |
| 3  | Sam   | 60000  | NULL      |
| 4  | Max   | 90000  | NULL      |
+----+-------+--------+-----------+
```

给定 `Employee` 表，编写一个 SQL 查询，该查询可以获取收入超过他们经理的员工的姓名。在上面的表格中，Joe 是唯一一个收入超过他的经理的员工。

```
+----------+
| Employee |
+----------+
| Joe      |
+----------+
```

### 答案

​	使用自己两次来查询结果

```mysql
SELECT NAME AS Employee
FROM Employee e1, Employee e2
WHERE e1.Id = e2.ManagerId AND e1.Salary > e2.Salary;
```

## [182. 查找重复的电子邮箱](https://leetcode-cn.com/problems/duplicate-emails/)

难度简单258收藏分享切换为英文接收动态反馈

SQL架构

编写一个 SQL 查询，查找 `Person` 表中所有重复的电子邮箱。

**示例：**

```
+----+---------+
| Id | Email   |
+----+---------+
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |
+----+---------+
```

根据以上输入，你的查询应返回以下结果：

```
+---------+
| Email   |
+---------+
| a@b.com |
+---------+
```

**说明：**所有电子邮箱都是小写字母。

### 答案

​	可以使用自连接，查询出现 Id 不同，但是 Email 重复的情况，这种查法记得去重

```mysql
SELECT DISTINCT p1.Email
FROM Person p1,Person p2
WHERE p1.Id != p2.Id AND p1.Email = p2.Email;
```

​	或者直接用 Email 分组，然后查询组内的数量

```mysql
SELECT Email
FROM Person
GROUP BY Email
HAVING count(Email) > 1;
```

## [183. 从不订购的客户](https://leetcode-cn.com/problems/customers-who-never-order/)

难度简单201收藏分享切换为英文接收动态反馈

SQL架构

某网站包含两个表，`Customers` 表和 `Orders` 表。编写一个 SQL 查询，找出所有从不订购任何东西的客户。

`Customers` 表：

```
+----+-------+
| Id | Name  |
+----+-------+
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |
+----+-------+
```

`Orders` 表：

```
+----+------------+
| Id | CustomerId |
+----+------------+
| 1  | 3          |
| 2  | 1          |
+----+------------+
```

例如给定上述表格，你的查询应返回：

```
+-----------+
| Customers |
+-----------+
| Henry     |
| Max       |
+-----------+
```

### 答案

​	第一种使用 NOT IN，查询 Orders 表里面所有的 Id，找出 Customers 有哪些列的 Id 不在这里面

```mysql
# Write your MySQL query statement below
SELECT Name AS Customers
FROM Customers c1
WHERE Id NOT IN (SELECT CustomerId FROM Orders);
```

​	但是有人说这种不走索引，效率低下，我就去网上查了一下，大部分人在说不走索引，但是又有人说走，有人说新版本走，我自己没有自己测试过，所以也不好说。但是尽量少用 NOT IN吧

​	第二种就是使用左外连接，将 Customers 与 Orders 连接，然后查询其中哪些的 Orders.Id 为null 即可

```mysql
SELECT c1.Name AS Customers
FROM Customers AS c1 left join Orders AS o1
ON o1.CustomerId = c1.Id
WHERE o1.Id IS NULL; 
```

​	从运行情况来说，这种确实比外连接高效率。

## [184. 部门工资最高的员工](https://leetcode-cn.com/problems/department-highest-salary/)

难度中等351收藏分享切换为英文接收动态反馈

SQL架构

`Employee` 表包含所有员工信息，每个员工有其对应的 Id, salary 和 department Id。

```
+----+-------+--------+--------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 70000  | 1            |
| 2  | Jim   | 90000  | 1            |
| 3  | Henry | 80000  | 2            |
| 4  | Sam   | 60000  | 2            |
| 5  | Max   | 90000  | 1            |
+----+-------+--------+--------------+
```

`Department` 表包含公司所有部门的信息。

```
+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+
```

编写一个 SQL 查询，找出每个部门工资最高的员工。对于上述表，您的 SQL 查询应返回以下行（行的顺序无关紧要）。

```
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| IT         | Jim      | 90000  |
| Sales      | Henry    | 80000  |
+------------+----------+--------+
```

**解释：**

Max 和 Jim 在 IT 部门的工资都是最高的，Henry 在销售部的工资最高。

### 答案

​	这道题学的最多的就是，IN 可以多匹配，不一定只能用一个列来匹配，这题还有一个坑，那就是部门可以为空，一开始用左连接错了

```mysql
SELECT d1.Name AS Department,e1.Name AS Employee, e1.Salary
FROM Employee e1 JOIN Department d1
ON e1.DepartmentID = d1.Id
WHERE (e1.DepartmentId,e1.Salary) IN(SELECT DepartmentId,Max(Salary) FROM Employee GROUP BY DepartmentId);
```

## [185. 部门工资前三高的所有员工](https://leetcode-cn.com/problems/department-top-three-salaries/)

难度困难437收藏分享切换为英文接收动态反馈

SQL架构

`Employee` 表包含所有员工信息，每个员工有其对应的工号 `Id`，姓名 `Name`，工资 `Salary` 和部门编号 `DepartmentId` 。

```
+----+-------+--------+--------------+
| Id | Name  | Salary | DepartmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 85000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |
| 7  | Will  | 70000  | 1            |
+----+-------+--------+--------------+
```

`Department` 表包含公司所有部门的信息。

```
+----+----------+
| Id | Name     |
+----+----------+
| 1  | IT       |
| 2  | Sales    |
+----+----------+
```

编写一个 SQL 查询，找出每个部门获得前三高工资的所有员工。例如，根据上述给定的表，查询结果应返回：

```
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| IT         | Randy    | 85000  |
| IT         | Joe      | 85000  |
| IT         | Will     | 70000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |
+------------+----------+--------+
```

**解释：**

IT 部门中，Max 获得了最高的工资，Randy 和 Joe 都拿到了第二高的工资，Will 的工资排第三。销售部门（Sales）只有两名员工，Henry 的工资最高，Sam 的工资排第二。

### 答案

​	dense_rank 函数的使用，在 over 里面定义编号规则，先通过 DepartmentId 分组，然后使用 Salary 倒序排序编号。

```mysql
SELECT d.Name AS Department, temp.Name AS Employee, Salary 
FROM (SELECT * ,DENSE_RANK() OVER(partition by DepartmentId order by Salary DESC) AS count FROM Employee) AS temp,Department AS d
WHERE WhERE d.Id = temp.Id AND count <= 3;
```

## [196. 删除重复的电子邮箱](https://leetcode-cn.com/problems/delete-duplicate-emails/)

难度简单349收藏分享切换为英文接收动态反馈

SQL架构

编写一个 SQL 查询，来删除 `Person` 表中所有重复的电子邮箱，重复的邮箱里只保留 **Id** *最小* 的那个。

```
+----+------------------+
| Id | Email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
| 3  | john@example.com |
+----+------------------+
Id 是这个表的主键。
```

例如，在运行你的查询语句之后，上面的 `Person` 表应返回以下几行:

```
+----+------------------+
| Id | Email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
+----+------------------+
```

 

**提示：**

- 执行 SQL 之后，输出是整个 `Person` 表。
- 使用 `delete` 语句。

### 答案

​	连接表删除，将 Id 比较大的删除了即可

```mysql
DELETE p1 
FROM Person p1,Person p2
WHERE p1.Id > p2.Id AND p1.Email = p2.Email
```

## [197. 上升的温度](https://leetcode-cn.com/problems/rising-temperature/)

难度简单190收藏分享切换为英文接收动态反馈

SQL架构

表 `Weather`

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| recordDate    | date    |
| temperature   | int     |
+---------------+---------+
id 是这个表的主键
该表包含特定日期的温度信息
```

 

编写一个 SQL 查询，来查找与之前（昨天的）日期相比温度更高的所有日期的 `id` 。

返回结果 **不要求顺序** 。

查询结果格式如下例：

```
Weather
+----+------------+-------------+
| id | recordDate | Temperature |
+----+------------+-------------+
| 1  | 2015-01-01 | 10          |
| 2  | 2015-01-02 | 25          |
| 3  | 2015-01-03 | 20          |
| 4  | 2015-01-04 | 30          |
+----+------------+-------------+

Result table:
+----+
| id |
+----+
| 2  |
| 4  |
+----+
2015-01-02 的温度比前一天高（10 -> 25）
2015-01-04 的温度比前一天高（20 -> 30）
```

### 答案

​	注意日期的处理，不能直接加减，要用函数，可以直接用 DATE_ADD 来加一个负数，或者使用 DATE_SUB 来处理。

```mysql
SELECT w2.id
FROM Weather w1,Weather w2
WHERE w1.recordDate = DATE_ADD(w2.recordDate,interval -1 day) AND w1.Temperature < w2.Temperature;
```

```mysql
# Write your MySQL query statement below
SELECT w2.id
FROM Weather w1,Weather w2
WHERE w1.recordDate = DATE_SUB(w2.recordDate,interval 1 day) AND w1.Temperature < w2.Temperature;
```

## 查询最晚入职员工的所有信息

​	有一个员工employees表简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612345140124/0BFB4D140D9C3E92AF681D9F9CB92D55)

建表语句如下:

```
CREATE TABLE `employees` (```emp_no` ``int``(``11``) NOT NULL, ```birth_date` date NOT NULL,```first_name` varchar(``14``) NOT NULL,```last_name` varchar(``16``) NOT NULL,```gender` ``char``(``1``) NOT NULL,```hire_date` date NOT NULL,``PRIMARY KEY (`emp_no`));
```

请你查找employees里最晚入职员工的所有信息，以上例子输出如下:![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612345279057/D2ABA1E2F5834850B16146F168AC5476)

### 答案

​	假如只需要输出一位最晚入职的员工，那么直接排序然后限制输出一个即可。如果是需要输出全部的，那么先求出最晚入职的，然后再输出所有这一天入职的人。

​	第一种：

```mysql
SELECT * from employees order by hire_date desc limit 0,1;
```

​	第二种：

```mysql
SELECT * from employees where hire_date = (select max(hire_date) from employees)
```

## 查找入职员工时间排名倒数第三

​	有一个员工employees表简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612345140124/0BFB4D140D9C3E92AF681D9F9CB92D55)

​	建表语句如下:

```
CREATE TABLE `employees` (```emp_no` ``int``(``11``) NOT NULL, ```birth_date` date NOT NULL,```first_name` varchar(``14``) NOT NULL,```last_name` varchar(``16``) NOT NULL,```gender` ``char``(``1``) NOT NULL,```hire_date` date NOT NULL,``PRIMARY KEY (`emp_no`));
```

​	请你查找employees里入职员工时间排名倒数第三的员工所有信息，以上例子输出如下:

![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612345180512/2A26AB183839E3A01C933AE5A75B6D2F)

### 答案

​	先查询出倒数第三入职的时间（记得去重），然后查询所有入职时间和这个时间相同的就好了。

​	查询第三入职的时间也可以使用 dense_rank() 函数

```mysql
SELECT * from employees 
where (hire_date,3) = 
    (select hire_date,dense_rank() over(order by hire_date desc) as `Rank`
     from employees
     limit 2,1)
```

​	如果不用函数也可以这么查

```mysql
SELECT * from employees
where hire_date = (select distinct hire_date from employees order by hire_date desc limit 2,1)
```

## 查找当前薪水详情以及部门编号...

​	有一个全部员工的薪水表salaries简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612509678777/C7D94B6C9124C45835451E89458FFC3E)



有一个各个部门的领导表dept_manager简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612509697144/24EC0AAEA6EF7D01BD63D4F9CCFC42BF)

建表语句如下:

```
CREATE TABLE `salaries` (```emp_no` ``int``(11) NOT NULL,```salary` ``int``(11) NOT NULL,```from_date` date NOT NULL,```to_date` date NOT NULL,``PRIMARY KEY (`emp_no`,`from_date`));`
`CREATE TABLE `dept_manager` (```dept_no` ``char``(4) NOT NULL,```emp_no` ``int``(11) NOT NULL,```to_date` date NOT NULL,``PRIMARY KEY (`emp_no`,`dept_no`));
```

请你查找各个部门领导薪水详情以及其对应部门编号dept_no，输出结果以salaries.emp_no升序排序，并且请注意输出结果里面dept_no列是最后一列，以上例子输入如下:

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612509718797/4AD4F3D1E977477D6DF98854EC10149D)

### 答案

​	简单的联表查询

```mysql
SELECT sa.*,dept_no
FROM salaries as sa,dept_manager as de
WHERE sa.emp_no = de.emp_no
ORDER BY emp_no
```

