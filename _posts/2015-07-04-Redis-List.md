---
layout: post
title:  "Redis随笔（6）-Redis 列表"
date:   2015-07-04 21:22:42
categories: Redis
excerpt: Redis随笔（6）-Redis 列表
---

* content
{:toc}


## 序

主要记录了redis列表的相关命令，如下

---

### redis列表命令

 * LPOP：

   *  删除，并返回保存列表在key的第一个元素。
        返回字符串，第一个元素的值，或者为nil 如果key不存在

   * 格式：LPOP KEY_NAME

 * BLPOP：

   *  命令用于删除和获取列表中的第一个元素，或阻塞直到有可用。 BLPOP命令只返回第一个元素(如果有的话)，或阻塞客户端对指定的时间执行任意命令。
        回复字符串，储存在key或nil值

   * 格式：BLPOP LIST1 LIST2 .. LISTN TIMEOUT

 * BRPOPLPUSH：

   *  用于从列表中弹出一个值，它推到另一个列表并返回它，或阻塞直到有可用。
        BRPOPLPUSH命令只返回最后一个元素，并插入到另一个列表中，如果有的话，或阻止客户端对指定的时间执行任意命令

   * 格式：BRPOPLPUSH LIST1 ANOTHER_LIST TIMEOU

 * LINDEX:

   * 用于获取在存储于列表的key索引的元素。索引是从0开始的，所以0表示第一个元素，1第二个元素等等。
        字符串回复，请求的元素，或者nil当索引超出范围

   * 格式： LINDEX KEY_NAME INDEX_POSITION

 * LINSERT:

   *  插入值在存储在key之前或参考值支点后。如果key不存在，它被认为是一个空列表，并没有进行任何操做

   * 格式：LINSERT KEY_NAME BEFORE EXISTING_VALUE NEW_VALUE
   <pre><code>
    redis 127.0.0.1:6379> RPUSH list1 "foo"
    (integer) 1
    redis 127.0.0.1:6379> RPUSH list1 "bar"
    (integer) 2
    redis 127.0.0.1:6379> LINSERT list1 BEFORE "bar" "Yes"
    (integer) 3
    redis 127.0.0.1:6379> LRANGE mylist 0 -1
    1) "foo"
    2) "Yes"
    3) "bar"
   </code></pre>

 * LLEN：

   * 返回存储在key列表的长度。如果key不存在，它被解释为一个空列表，则返回0

   * 格式：LLEN KEY_NAME

 * LPUSH：

   * 在保存在key列表的头部插入所有指定的值。如果key不存在，则执行推操作之前创建的空列表。当key持有的值不是列表，则返回一个错误

   * 格式：LPUSH KEY_NAME VALUE1.. VALUEN

 * LPUSHX：

   * 插入存储在列表的头部的键值，仅当键已经存在，并持有(它是)列表

   * 格式：LPUSHX KEY_NAME VALUE1.. VALUEN

 * LRANGE：

   * 返回存储在key列表的特定元素。偏移量开始和停止是从0开始的索引，0是第一元素(该列表的头部)，1是列表的下一个元素。
        这些偏移量也可以是表示开始在列表的末尾偏移负数。例如，-1是该列表的最后一个元素，-2倒数第二个

   * 格式： LRANGE KEY_NAME START END

 * LSET：

   * 在索引值处设置新的的列表元素

   * 格式：  LSET KEY_NAME INDEX VALUE

 * LTRIM：

   * 命令修剪现有列表，使其包含指定的元素仅在指定的范围内

   * 格式：LTRIM KEY_NAME START STOP

 * LREM：

   * 在指定Key关联的链表中，删除前count个值等于value的元素。如果count大于0，从头向尾遍历并删除，如果count小于0，则从尾向头遍历并删除。
        如果count等于0，则删除链表中所有等于value的元素

   * 格式：LREM key count value

