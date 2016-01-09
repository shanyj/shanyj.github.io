---
layout: post
title:  "Mysql 基础语句&限制语句"
date:   2015-11-1 21:23:41
categories: Mysql
excerpt: Mysql 基础语句&限制语句
---

* content
{:toc}


## 序

这里主要记录下Mysql中的基础操作语句和一些限制语句

---

### 基础语句

 * **SELECT**

    * SELECT 列名称 FROM 表名称

      SELECT LastName,FirstName FROM Persons

    * SELECT 列名称 FROM 表名称 WHERE 列 运算符 值

      SQL使用单引号来环绕文本值,如果是数值，不使用引号。

    * 文本值：

      这是正确的：

      SELECT * FROM Persons WHERE FirstName='Bush'

      这是错误的：

      SELECT * FROM Persons WHERE FirstName=Bush

    * 数值：

      这是正确的：

      SELECT * FROM Persons WHERE Year>1965

      这是错误的：

      SELECT * FROM Persons WHERE Year>'1965'

    * AND 和 OR 可在 WHERE 子语句中把两个或多个条件结合起来

      SELECT * FROM Persons WHERE (FirstName='Thomas' OR FirstName='William')
    AND LastName='Carter

 * **INSERT INTO**

    * INSERT INTO 表名称 VALUES (值1, 值2,....)

    * INSERT INTO table_name (列1, 列2,...) VALUES (值1, 值2,....) AND LastName='Carter

 * **UPDATE**

    * UPDATE 表名称 SET 列名称 = 新值 WHERE 列名称 = 某值

    * 更新某一行中的若干列

      UPDATE Person SET Address = 'Zhongshan 23', City = 'Nanjing'
    WHERE LastName = 'Wilson'

 * **DELETE**

    * DELETE FROM 表名称 WHERE 列名称 = 值

    * 删除整个表数据

      DELETE * FROM table_name

 * **DISTINCT**

    * 关键词 DISTINCT 用于返回唯一不同的值

    * SELECT DISTINCT 列名称 FROM 表名称

 * **ORDER BY**

    * ORDER BY 语句用于根据指定的列对结果集进行排序

    * 以逆字母顺序显示公司名称，并以数字顺序显示顺序号：

      SELECT Company, OrderNumber FROM Orders ORDER BY Company DESC, OrderNumber ASC

---

### 基础语句

 * **LIMIT**

    * SELECT column_name(s) FROM table_name LIMIT number setp

 * **LIKE**

    * SELECT column_name(s) FROM table_name WHERE column_name LIKE pattern

    * pattern格式：    %	： 替代一个或多个字符，
    _ ： 仅替代一个字符，
    [charlist] ： 字符列中的任何单一字符，
    [^charlist]或[!charlist] ： 不在字符列中的任何单一字符

 * **IN**

    * SELECT column_name(s) FROM table_name WHERE column_name IN (value1,value2,...)

 * **BETWEEN**

    * SELECT column_name(s) FROM table_name WHERE column_name BETWEEN value1 AND value2

    * 如需使用上面的例子显示范围之外的人，请使用 NOT 操作符：

      SELECT * FROM Persons WHERE LastName NOT BETWEEN 'Adams' AND 'Carter'

 * **AS**

    * 为列名称和表名称指定别名

    * 列的 SQL Alias 语法

      SELECT column_name AS alias_name FROM table_name

    * 使用表名称别名

      SELECT po.OrderID, p.LastName FROM Persons AS p, Product_Orders AS po WHERE p.LastName='Adams' AND p.FirstName='John'