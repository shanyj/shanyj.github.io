---
layout: post
title:  "Redis 字符串"
date:   2015-06-20 21:11:46
categories: Redis
excerpt: Redis 字符串
---

* content
{:toc}


## 序

主要记录了redis字符串的相关命令，如下

---

### redis字符串命令

 * SET：

   * 设置在Redis键部分字符串值

   * 格式：SET KEY_NAME VALUE

   * 在SET命令有许多可供选项，即修改命令的行为。SET KEY VALUE \[EX seconds\] \[PX milliseconds\] \[NX\|XX\]

    EX seconds - 设置指定的到期时间，单位为秒。

    PX milliseconds - 设置指定到期时间，单位为毫秒。

    NX - 只有设置键，如果它不存在。

    XX - 只有设置键，如果它已经存在。

 * GET：

   * 获取存储在指定的键的值。如果键不存在，那么返回nil。如果返回值不是字符串，则返回错误

   * 格式：GET KEY_NAME

 * GETRANGE：

   * 获取存储在键字符串值。返回简单的字符串答复

   * 格式：GETRANGE KEY_NAME start end
   <pre><code>
    redis 127.0.0.1:6379> GETRANGE mykey 0 -1
    "This is my test key"
   </code></pre>

 * GETSET：

   * 设置指定的在Redis的键的字符串值，并返回其原来的值。回复简单的字符串，键的旧值。如果键不存在，那么返回nil

   * 格式：GETSET KEY_NAME VALUE

 * GETBIT：

   * 获取在存储在键串值偏移的比特值

   * 格式：GETBIT KEY_NAME OFFSET

 * MGET：

   * 获取所有指定键的值。对于未持有一个字符串值，或者每一个键不存在，返回特殊值为nil

   * 格式：MGETBIT KEY_NAME1 KEY_NAME2
   <pre><code>
    redis 127.0.0.1:6379> SET key1 "hello"
    OK
    redis 127.0.0.1:6379> SET key2 "world"
    OK
    redis 127.0.0.1:6379> MGET key1 key2 someOtherKey
    1) "Hello"
    2) "World"
    3) (nil)
    </code></pre>

 * SETEX：

   * 设置一些字符串值，在Redis的键指定的超时时间内。简单的字符串回复OK，如果值被设置在键，否则如果值不设置为null

   * 格式： SETEX KEY_NAME TIME VALUE

 * SETNX：

   *  SETNX命令是用来设置在Redis的键部分字符串值，如果key没有在Redis的存在。返回 1, 如果该键设置。0, 如果该键不被设置
   <pre><code>
    redis 127.0.0.1:6379> SETNX mykey redis
    (integer) 1
    redis 127.0.0.1:6379> SETNX mykey mongodb
    (integer) 0
    redis 127.0.0.1:6379> GET mykey
    "redis"
    </code></pre>

 * SETRANGE：

   * 用来改写字符串的一部分，在键的指定开始的偏移量。整数回复字符串在它被命令修改后的长度
    <pre><code>
    redis 127.0.0.1:6379> SET key1 "Hello World"
    OK
    redis 127.0.0.1:6379> SETRANGE key1 6 "Redis"
    (integer) 11
    redis 127.0.0.1:6379> GET key1
    "Hello Redis"
    </code></pre>

 * STRLEN：

   * 获取存储在key字符串值的长度，回复整数，字符串key长度，或0表示key不存在

   * 格式：STRLEN KEY_NAME

 * MSET：

   * MSET命令用于设定多个键，以及多个值

   * 格式：MSET key1 value1 key2 value2 .. keyN valueN

 * MSETNX：

   * MSETNX命令用于设置多个键以及多个值，仅当没有一个已存在。如果从当前操作的任何一个存在，那么MSETNX不执行任何操作。1, 如果所有的键都在Redis设置。0, 如果没有key在Redis设置

   * 格式：MSETNX key1 value1 key2 value2 .. keyN valueN

 * PSETEX：

   * 用于设置key的值，随着时间以毫秒为单位过期

   * 格式：PSETEX key1 EXPIRY_IN_MILLISECONDS value1

 * INCR&DECR：

   * 递增/减key的整数值。如果该key不存在，它被设置为0执行操作之前。如果key包含了错误类型的值或包含不能被表示为整数，字符串，则返回错误。 回复整数，键增量后的值

   * 格式：INCR KEY_NAME     DECR KEY_NAME

 * INCRBY&DECRBY：

   * 以某步数递增/减key的整数值

   * 格式：INCRBY KEY_NAME INCR_AMOUNT     DECRBY KEY_NAME DECREMENT_AMOUNT

 * APPEND：

   *  用来添加键的一些值

   * 格式：APPEND KEY_NAME NEW_VALUE