---
layout: post
title:  "Redis 哈希"
date:   2015-06-30 20:45:21
categories: Redis
excerpt: Redis 哈希
---

* content
{:toc}


## 序

主要记录了redis哈希的相关命令，如下

---

### redis哈希命令

 * HDEL：

   * 从存储在键散列删除指定的字段，如果没有这个哈希中存在指定的字段将被忽略。如果键不存在，它将被视为一个空的哈希与此命令将返回0

   * 格式：HDEL KEY_NAME FIELD1.. FIELDN
   <pre><code>
    redis 127.0.0.1:6379> HSET myhash field1 "foo"
    (integer) 1
    redis 127.0.0.1:6379> HDEL myhash field1
    (integer) 1
    redis 127.0.0.1:6379> HDEL myhash field2
    (integer) 1
   </code></pre>

 * HEXISTS：

   * 检查哈希字段是否存在。1, 如果哈希包含字段。0 如果哈希不包含字段，或key不存在

   * 格式：HEXISTS KEY_NAME FIELD_NAME

 * HGET：

   * 获取与字段中存储的键哈希相关联的值

   * 格式：HGET KEY_NAME FIELD_NAME

 * HGETALL：

   * 获取存储在键的散列的所有字段和值

   * 格式：HGETALL KEY_NAME

 * HMGET:

   * 批量获取存储在键的散列的值

   * 格式：HMGET KEY_NAME FIELD1...FIELDN
   <pre><code>
    redis 127.0.0.1:6379> HSET myhash field1 "foo"
    (integer) 1
    redis 127.0.0.1:6379> HSET myhash field2 "bar"
    (integer) 1
    redis 127.0.0.1:6379> HMGET myhash field1 field2 nofield
    1) "foo"
    2) "bar"
    3) (nil)
   </code></pre>

 * HINCRBY:

   * 增加存储在字段中存储由增量键哈希的数量。如果键不存在，新的key被哈希创建。如果字段不存在，值被设置为0之前进行操作。回复整数，字段的增值操作后的值

   * 格式：HINCRBY KEY_NAME FIELD_NAME INCR_BY_NUMBER
   <pre><code>
    redis 127.0.0.1:6379> HSET myhash field1 20
    (integer) 1
    redis 127.0.0.1:6379> HINCRBY myhash field 1
    (integer) 21
    redis 127.0.0.1:6379> HINCRBY myhash field -1
    (integer) 20
   </code></pre>

 * HKEYS:

   * 用来获取所有字段名保存在键的哈希值。回复数组，哈希字段列表或者当key不存在是为一个空的列表

   * 格式：HKEYS KEY_NAME
   <pre><code>
    redis 127.0.0.1:6379> HSET myhash field1 "foo"
    (integer) 1
    redis 127.0.0.1:6379> HSET myhash field2 "bar"
    (integer) 1
    redis 127.0.0.1:6379> HKEYS myhash
    1) "field1"
    2) "field2"
   </code></pre>

 * HLEN:

   * 用于获取包含存储于键的散列的字段的数量。回复整数哈希字段数或0当键不存在

   * 格式：HLEN KEY_NAME

 * HVALS:

   * 获取在存储于 key的散列的所有值

   * 格式：HVALS KEY_NAME

 * HMSET:

   * 设置指定字段各自的值，在存储于键的散列。此命令将覆盖哈希任何现有字段。如果键不存在，新的key由哈希创建

   * 格式： HMSET KEY_NAME FIELD1 VALUE1 ...FIELDN VALUEN
   <pre><code>
    redis 127.0.0.1:6379> HSET myhash field1 "foo" field2 "bar"
    OK
    redis 127.0.0.1:6379> HGET myhash field1
    "foo"
    redis 127.0.0.1:6379> HMGET myhash field2
    "bar"
   </code></pre>

 * HSET:

   *  存储的关键值的散列设置字段。如果键不存在，新的key由哈希创建。如果字段已经存在于哈希值那么将被覆盖。
          1 如果字段是哈希值和一个新字段被设置。0 如果字段已经存在于哈希并且值被更新

   * 格式：HSET KEY_NAME FIELD VALUE

 * HSETNX:

   *  用于在存储的关键值的散列设置字段，只有在字段不存在。如果键不存在，新的key会被哈希创建。如果字段已经存在，该操作没有任何影响。
            1 如果字段是哈希值和一个新字段被设置。0 如果字段已经存在于哈希那么没有执行任何操作

   * 格式：HSETNX KEY_NAME FIELD VALUE
