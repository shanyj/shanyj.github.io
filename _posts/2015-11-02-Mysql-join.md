---
layout: post
title:  "Mysql Join"
date:   2015-11-2 20:13:46
categories: Mysql
excerpt: Mysql Join
---

* content
{:toc}


## 序

这里主要记录下Mysql中的Join语句的一些用法

---

### join语句

 * **JOIN**

   * 我们需要从两个或更多的表中获取结果。我们就需要执行 join

   * SELECT Persons.LastName, Persons.FirstName, Orders.OrderNo
    FROM Persons
    JOIN Orders
    ON Persons.Id_P = Orders.Id_P
    ORDER BY Persons.LastName

 * **INNER JOIN**

   * INNER JOIN 与 JOIN 是相同的

 * **LEFT JOIN**

   * LEFT JOIN 关键字会从左表 (table_name1) 那里返回所有的行，即使在右表 (table_name2) 中没有匹配的行

   * SELECT column_name(s)
    FROM table_name1
    LEFT JOIN table_name2
    ON table_name1.column_name=table_name2.column_name

   * 只要左边表有column_name就取出，如右边表有一样的column_name则合并，没有则留空


 * **RIGHT JOIN**

   * RIGHT JOIN 关键字会从右表 (table_name2) 那里返回所有的行，即使在左表 (table_name1) 中没有匹配的行

   * SELECT column_name(s)
    FROM table_name1
    RIGHT JOIN table_name2
    ON table_name1.column_name=table_name2.column_name


 * **FULL JOIN**

   * FULL JOIN关键字会从左右表那里返回所有的行

   *  SELECT column_name(s)
    FROM table_name1
    FULL JOIN table_name2
    ON table_name1.column_name=table_name2.column_name


 * **UNION**

   * 请注意，UNION 内部的 SELECT 语句必须拥有相同数量的列。列也必须拥有相似的数据类型。同时，每条 SELECT 语句中的列的顺序必须相同

   *  SELECT column_name(s) FROM table_name1
    UNION
    SELECT column_name(s) FROM table_name2

   * 使增加了结果的行数，并没有增加列数，所以列数必须相同


