---
layout: post
title:  "Redis 有序集合"
date:   2015-07-09 20:42:05
categories: Redis
excerpt: Redis 有序集合
---

* content
{:toc}


## 序

主要记录了redis有序集合的相关命令，如下

---

### redis有序集合命令

 * ZADD：

   *  添加所有指定的成员指定的分数存放在键的有序集合。它可以指定多个分/成员对。
        如果指定的成员已经是有序集合中的一员，分数被更新，并在合适的位置插入元素，以确保正确的顺序

   * 格式： ZADD KEY_NAME SCORE1 VALUE1.. SCOREN VALUEN

 * ZCARD：

   * 返回在指定的键存储在集合中的元素的数量

   * 格式： ZCARD KEY_NAME

 * ZCOUNT：

   * 返回最小值和最大值之间的得分键的有序集合的元素个数

   * 格式：ZCOUNT myzset (1 3

 * ZINTERSTORE:

   * 返回最小值和最大值之间的得分键的有序集合的元素个数
    <pre><code>
     # 有序集 mid_test
    redis 127.0.0.1:6379> ZADD mid_test 70 "Li Lei"
    (integer) 1
    redis 127.0.0.1:6379> ZADD mid_test 70 "Han Meimei"
    (integer) 1
    redis 127.0.0.1:6379> ZADD mid_test 99.5 "Tom"
    (integer) 1

    # 另一个有序集 fin_test
    redis 127.0.0.1:6379> ZADD fin_test 88 "Li Lei"
    (integer) 1
    redis 127.0.0.1:6379> ZADD fin_test 75 "Han Meimei"
    (integer) 1
    redis 127.0.0.1:6379> ZADD fin_test 99.5 "Tom"
    (integer) 1

    # 交集
    redis 127.0.0.1:6379> ZINTERSTORE sum_point 2 mid_test fin_test
    (integer) 3

    # 显示有序集内所有成员及其分数值
    redis 127.0.0.1:6379> ZRANGE sum_point 0 -1 WITHSCORES
    1) "Han Meimei"
    2) "145"
    3) "Li Lei"
    4) "158"
    5) "Tom"
    6) "199"
    </code></pre>

 * ZRANGE:

   * 返回存储在关键的排序元素集合在指定的范围

   * 格式：ZRANGE KEY START STOP [WITHSCORES]

 * ZRANGEBYLEX:

   * 命令返回存储在键的排序集合在指定的范围元素。该元素被认为是从最低到最高的分值进行排序

   * 格式：ZRANGEBYLEX key min max [LIMIT offset count]
   <pre><code>
     redis 127.0.0.1:6379> ZADD myzset 0 a 0 b 0 c 0 d 0 e
    (integer) 5
    redis 127.0.0.1:6379> ZADD myzset 0 f 0 g
    (integer) 2
    redis 127.0.0.1:6379> ZRANGEBYLEX myzset - [c
    1) "a"
    2) "b"
    3) "c"
    redis 127.0.0.1:6379> ZRANGEBYLEX myzset - (c
    1) "a"
    2) "b"
   </code></pre>

 * ZRANGEBYSCORE：

   * 返回的有序集合在最小值和最大值(包括得分等于最小或最大元素)之间的分数键中的所有元素

   * 格式：ZRANGEBYSCORE key min max [WITHSCORES]
   <pre><code>
    redis 127.0.0.1:6379> ZADD myzset 0 a 1 b 2 c 3 d 4 e
    (integer) 5
    redis 127.0.0.1:6379> ZADD myzset 5 f 6 g
    (integer) 2
    redis 127.0.0.1:6379> ZRANGEBYSCORE myzset 1 2
    1) "b"
    2) "c"
    redis 127.0.0.1:6379> ZRANGEBYSCORE myzset (1 2
    1) "b"
   </code></pre>

 * ZRANK：

   * 返回成员的有序集合保存在key，由低到高的分数顺序排名

   * 格式：ZRANK key member

 * ZREM：

   * 从有序集合存储在键删除指定成员

   * 格式：ZREM key member [member ...]

 * ZREMRANGEBYLEX：

   * 删除有序集合存储在由最小和最大指定的字典范围之间的所有键元素

   * 格式：ZREMRANGEBYLEX key min max

 * ZREMRANGEBYRANK：

   * 命令删除有序集合保存在key开始和结束的排序所有元素。无论是开始和结束都以0基础索引，其中0是得分最低的元素。
        这些索引可以是负数，在那里它们表明起始于具有最高得分的元素偏移

   * 格式：ZREMRANGEBYRANK key start stop

 * ZREMRANGEBYSCORE：

   * 删除的有序集合保存在key的最小值和最大值(含)之间的分数的所有元素

   * 格式：ZREMRANGEBYSCORE key min max

 * ZREVRANGE：

   * 返回存储在键的排序元素集合在指定的范围。该元素被认为是从最高到最低得分排序。降字典顺序用于以相等的分数的元素

   * 格式：ZREVRANGE key min max

 * ZSCORE：

   * 返回成员的有序集合在键比分

   * 格式：ZSCORE key member

