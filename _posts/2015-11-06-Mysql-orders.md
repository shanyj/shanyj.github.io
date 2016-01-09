---
layout: post
title:  "Mysql 库&表&属性命令"
date:   2015-11-6 19:52:26
categories: Mysql
excerpt: Mysql 库&表&属性命令
---

* content
{:toc}


## 序

这里主要记录下Mysql中的Mysql库&表&属性命令的一些用法

---

### 库&表&属性命令

 * **SELECT INTO**

   * SELECT INTO 语句从一个表中选取数据，然后把数据插入另一个表中

   * SELECT column_name(s) INTO new_table_name [IN externaldatabase] FROM old_tablename

   * IN 子句可用于向另一个数据库中拷贝表

   * SELECT * INTO Persons IN 'Backup.mdb' FROM Persons

 * **CREATE DATABASE**

   * CREATE DATABASE 用于创建数据库

   * CREATE DATABASE database_name

 * **CREATE TABLE**

   * CREATE DATABASE 用于创建数据库
     <pre><code>
    CREATE TABLE 表名称
    (
    列名称1 数据类型,
    列名称2 数据类型,
    列名称3 数据类型,
    ....
    )
    </code></pre>

 * **NOT NULL**

   * NOT NULL 约束强制列不接受 NULL 值
     <pre><code>
    CREATE TABLE Persons
    (
    Id_P int NOT NULL,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Address varchar(255),
    City varchar(255)
    )
    </code></pre>

 * **UNIQUE**

   * UNIQUE 约束唯一标识数据库表中的每条记录
     <pre><code>
    CREATE TABLE Persons
    (
    Id_P int NOT NULL,
    City varchar(255),
    UNIQUE (Id_P)
    )
    </code></pre>

   * 如果需要命名 UNIQUE 约束，以及为多个列定义 UNIQUE 约束
     <pre><code>
    CREATE TABLE Persons
    (
    Id_P int NOT NULL,
    City varchar(255),
    CONSTRAINT uc_PersonID UNIQUE (Id_P,LastName)
    )
    </code></pre>

   * 当表已被创建时，如需在 "Id_P" 列创建 UNIQUE 约束，请使用下列 SQL：

     ALTER TABLE Persons ADD UNIQUE (Id_P)

   * 如需命名 UNIQUE 约束，并定义多个列的 UNIQUE 约束，请使用下面的 SQL 语法：

     ALTER TABLE Persons ADD CONSTRAINT uc_PersonID UNIQUE (Id_P,LastName)

   * 如需撤销 UNIQUE 约束，请使用下面的 SQL:

     ALTER TABLE Persons DROP INDEX uc_PersonID

 * **PRIMARY KEY**

   * PRIMARY KEY 约束唯一标识数据库表中的每条记录
     <pre><code>
    CREATE TABLE Persons
    (
    Id_P int NOT NULL,
    City varchar(255),
    PRIMARY KEY (Id_P)
    )
    </code></pre>

 * **FOREIGN KEY**

   * 一个表中的 FOREIGN KEY 指向另一个表中的 PRIMARY KEY
     <pre><code>
    CREATE TABLE Orders
    (
    Id_O int NOT NULL,
    Id_P int,
    PRIMARY KEY (Id_O),
    FOREIGN KEY (Id_P) REFERENCES Persons(Id_P)
    )
    </code></pre>

 * **CHECK**

   * CHECK 约束用于限制列中的值的范围
     <pre><code>
     CREATE TABLE Persons
    (
    Id_P int NOT NULL,
    City varchar(255),
    CHECK (Id_P>0)
    )
    </code></pre>

 * **DEFAULT**

   * 创建 DEFAULT 约束
     <pre><code>
     CREATE TABLE Persons
    (
    Id_P int NOT NULL,
    City varchar(255) DEFAULT 'Sandnes'
    )
    </code></pre>

   * 如果在表已存在的情况下为 "City" 列创建 DEFAULT 约束，请使用下面的 SQL

     ALTER TABLE Persons
    ALTER City SET DEFAULT 'SANDNES'

   * 如需撤销 DEFAULT 约束，请使用下面的 SQL

     ALTER TABLE Persons
    ALTER City DROP DEFAULT

 * **CREATE INDEX**

   * 在表上创建一个简单的索引

     CREATE INDEX index_name
    ON table_name (column_name)

   * 在表上创建一个唯一的索引。唯一的索引意味着两个行不能拥有相同的索引值。

     CREATE UNIQUE INDEX index_name
    ON table_name (column_name)

   * 假如您希望索引不止一个列，您可以在括号中列出这些列的名称，用逗号隔开：

     CREATE INDEX PersonIndex
    ON Person (LastName, FirstName)

   * 删除

     ALTER TABLE table_name DROP INDEX index_name

 * **DROP**

   * DROP TABLE 语句用于删除表（表的结构、属性以及索引也会被删除）：

     DROP TABLE 表名称

   * DROP DATABASE 语句用于删除数据库:

     DROP DATABASE 数据库名称

   * SQL TRUNCATE TABLE 语句

     如果我们仅仅需要除去表内的数据

     TRUNCATE TABLE 表名称

 * **ALTER**

   * 如需在表中添加列，请使用下列语法

     ALTER TABLE table_name
    ADD column_name datatype

   * 要删除表中的列，请使用下列语法

     ALTER TABLE table_name
    DROP COLUMN column_name

   * 要改变表中列的数据类型，请使用下列语法

     ALTER TABLE table_name
    ALTER COLUMN column_name datatype

 * **AUTO INCREMENT**

   * 通常希望在每次插入新记录时，自动地创建主键字段的值
     <pre><code>
     CREATE TABLE Persons
    (
    P_Id int NOT NULL AUTO_INCREMENT,
    LastName varchar(255) NOT NULL,
    FirstName varchar(255),
    Address varchar(255),
    City varchar(255),
    PRIMARY KEY (P_Id)
    )
    </code></pre>

   * 默认地，AUTO_INCREMENT 的开始值是 1，每条新记录递增 1

   * 要让 AUTO_INCREMENT 序列以其他的值起始，请使用下列 SQL 语法：

     ALTER TABLE Persons AUTO_INCREMENT=100

 * **VIEW**

   * 视图是基于 SQL 语句的结果集的可视化的表

     CREATE VIEW view_name AS
    SELECT column_name(s)
    FROM table_name
    WHERE condition


   * 使用下面的语法来更新视图

     CREATE OR REPLACE VIEW view_name AS
    SELECT column_name(s)
    FROM table_name
    WHERE condition

   * 删除

     DROP VIEW view_name
