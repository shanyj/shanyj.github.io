---
layout: post
title:  "Mysql 函数"
date:   2015-11-11 20:51:25
categories: Mysql
excerpt: Mysql 函数
---

* content
{:toc}


## 序

这里主要记录下Mysql函数的一些用法

---

### 聚合函数

 * 聚合函数都是在先筛选完了再在结果上再次筛选,在GROUP BY之后,在where条件之后

 * **AVG()**

   * SELECT Customer FROM Orders
        WHERE OrderPrice>(SELECT AVG(OrderPrice) FROM Orders)

 * **COUNT()**

   * COUNT(column_name) 函数返回指定列的值的数目（NULL 不计入）

   * SELECT COUNT(column_name) FROM table_name

   * COUNT(DISTINCT column_name) 函数返回指定列的不同值的数目：

     SELECT COUNT(DISTINCT column_name) FROM table_name

   * COUNT(*) 函数返回表中的记录数：

     SELECT COUNT(*) FROM table_name

 * **FIRST()&LAST()**

   * SELECT FIRST(column_name) FROM table_name

   * SELECT LAST(column_name) FROM table_name

 * **MAX()&MIN()**

   * SELECT MAX(column_name) FROM table_name

   * SELECT MIN(column_name) FROM table_name

 * **SUM()**

   * SUM 函数返回数值列的总数（总额）

   * SELECT SUM(column_name) FROM table_name

 * **GROUP BY**

   * SELECT Customer,SUM(OrderPrice) FROM Orders
        GROUP BY Customer

   * GROUP BY相当于多个where语句的结果合并起来

   * 我们也可以对一个以上的列应用 GROUP BY 语句，就像这样：

     SELECT Customer,OrderDate,SUM(OrderPrice) FROM Orders
        GROUP BY Customer,OrderDate

 * **HAVING**

   * having在聚合函数之后，where在聚合函数之前

   * WHERE 关键字无法与合计函数一起使用

   * SELECT Customer,SUM(OrderPrice) FROM Orders
        WHERE Customer IN ('a','b')
        GROUP BY Customer
        HAVING SUM(OrderPrice)<2000

---

### Scalar 函数

 * Scalar 函数的操作面向某个单一的值，并返回基于输入值的一个单一的值，可以和where连用

 * UCASE()

   * SELECT UCASE(column_name) FROM table_name

 * LCASE()

   * SELECT LCASE(column_name) FROM table_name

 * MID()

   * SELECT MID(column_name,start[,length]) FROM table_name

 * LEN()

   * SELECT LEN(column_name) FROM table_name

 * ROUND()

   * SELECT ROUND(column_name,decimals) FROM table_name

   * column_name:必需。要舍入的字段。

   * decimals:必需。规定要返回的小数位数。

---

### 日期相关

 * NOW()    返回当前的日期和时间

 * CURDATE()	返回当前的日期

 * CURTIME()	返回当前的时间

 * DATE()	提取日期或日期/时间表达式的日期部分

   * SELECT ProductName, DATE(OrderDate) AS OrderDate FROM Orders

 * EXTRACT()	返回日期/时间按的单独部分，比如年、月、日、小时、分钟等等

   * EXTRACT(unit FROM date) unit可以是year、date等

 * DATE_ADD()	给日期添加指定的时间间隔

   * DATE_ADD(date,INTERVAL expr type)date 参数是合法的日期表达式。expr 参数是您希望添加的时间间隔。

   * SELECT OrderId,DATE_ADD(OrderDate,INTERVAL 2 DAY) AS OrderPayDate FROM Orders

 * DATE_SUB()	从日期减去指定的时间间隔

 * DATEDIFF()	返回两个日期之间的天数

   * SELECT DATEDIFF('2008-12-30','2008-12-29') AS DiffDate

 * DATE_FORMAT()	用不同的格式显示日期/时间

   * DATE_FORMAT(date,format) date 参数是合法的日期。format 规定日期/时间的输出格式。

