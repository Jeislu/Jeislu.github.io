---
title: MySQL学习
date: 2020-12-05 23:05:12
tags: 
 - MySQL
---

## 前言

​	本文重用，用来记录 MySQL 中我可能会忘的知识点以及常用函数（有些可能是8.0才有的，会标注)

<!-- more -->

## 方法

### 排序

- RANK并列跳跃排名，并列即相同的值，相同的值保留重复名次，遇到下一个不同值时，跳跃到总共的排名。
- DENSE_RANK并列连续排序，并列即相同的值，相同的值保留重复名次，遇到下一个不同值时，依然按照连续数字排名。
- ROW_NUMBER连续排名，即使相同的值，依旧按照连续数字进行排名。

例如排序 {300，200，100，300，400}：

RANK 会得到{1，2，3，3，5}，因为300有两个，所以到400的时候，排名会 + 2

DENSE_RANK 会得到{1，2，3，3，4}，就算 300 有两个，还是排一个位置

ROW_NUMBER 会得到{1，2，3，4，5}，就算有两个相同的 300，他们之间也必须排序。

这三个函数都需要搭配 OVER 使用，在 OVER 里面表明排序规则，例如

```mysql
SELECT score, DENSE_RANK() OVER (ORDER BY Score DESC) AS 'Rank'
FROM Scores;
```

### 日期处理方法

​	DATE_ADD(date,INTERVAL expr type) 函数向日期添加指定的时间间隔。

​	DATE_SUB(date,INTERVAL expr type) 函数向日期减少指定的时间间隔。（也可以通过日期增加函数来增加负数实现改功能）

	date 参数是合法的日期表达式。

​	expr参数是您希望添加的时间间隔。

​	type 参数可以是下列值：

```java
MICROSECOND            // 微秒
SECOND                 // 秒  
MINUTE                 // 分
HOUR                   // 小时
DAY                    // 日期
WEEK                   // 周
MONTH                  // 月
QUARTER                // 季
YEAR                   // 年
// 后面这几个就是联合使用而已，联合使用需要把值用双引号括起来
// 例如: ELECT DATE_ADD('2100-12-31 23:59:59', INTERVAL '1:1' MINUTE_SECOND);
SECOND_MICROSECOND     
MINUTE_MICROSECOND
MINUTE_SECOND
HOUR_MICROSECOND
HOUR_SECOND
HOUR_MINUTE
DAY_MICROSECOND
DAY_SECOND
DAY_MINUTE
DAY_HOUR
YEAR_MONTH
```

