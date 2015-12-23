---
layout: post
title:  "Redis随笔（10）-Redis HyperLogLog&订阅&事务"
date:   2015-07-18 21:31:01
categories: Redis
excerpt: Redis随笔（10）-Redis HyperLogLog&订阅&事务
---

* content
{:toc}


## 序

主要记录了Redis的HyperLogLog&订阅&事务相关的一些命令

---

### HyperLogLog

 *  简介：Redis HyperLogLog 是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定 的、并且是很小的
 每个 HyperLogLog 键只需要花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基 数
 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素

 * PFADD：

   *  PFADD命令将所有元素的参数保存在指定为第一个参数的键名的HyperLogLog数据结构

   * 格式：PFADD KEY_NAME ELEMENTS_TO_BE_ADDED

 * PFCOUNT：

   * PFCOUNT命令用于获得近似的基数由存储在指定变量的HyperLogLog数据结构进行计算

   * 当PFCOUNT命令使用多个键，则返回HyperLogLog联合近似基数

 * PPFMERGE：

   * PFMERGE命令用于将多个HyperLogLog值转换成一个唯一的值将接近源HyperLogLog结构遵守集合并集的基数

   * PFMERGE KEY1 KEY@... KEYN

 * 实例：

    <pre><code>
    redis 127.0.0.1:6379> PFADD tutorials "redis"
    1) (integer) 1
    redis 127.0.0.1:6379> PFADD tutorials "mongodb"
    1) (integer) 1
    redis 127.0.0.1:6379> PFADD tutorials "mysql"
    1) (integer) 1
    redis 127.0.0.1:6379> PFCOUNT tutorials
    (integer) 3
    </code></pre>

---

### 订阅

 * PSUBSCRIBE：

   * PSUBSCRIBE命令用于订阅频道匹配给定的模式

   * 格式： PSUBSCRIBE CHANNEL_NAME_OR_PATTERN [PATTERN...]

   * 下面列出了一些Redis的支持模式

  h\?llo匹配了hello, hallo和hxllo

  h\*llo 匹配了hllo和heeeello

  h\[ae\]llo 匹配了 hello 和 hallo, 但不是hillo

 * PUBLISH：

   * 发布消息，返回整数：该接收到的消息的客户端数

   * 格式：PUBLISH channel message

 * PUNSUBSCRIBE：

   * PUNSUBSCRIBE命令从给定的模式退订客户端，或从所有如果没有给出。如果未指定任何模式，客户端是从所有先前认阅的模式退订

   * 格式：PUNSUBSCRIBE [pattern [pattern ...]]

 * SUBSCRIBE：

   * SUBSCRIBE命令所预订客户的指定的通道

   * 格式：SUBSCRIBE channel [channel ...]

 * UNSUBSCRIBE:

   * UNSUBSCRIBE指令从给定的信道退订客户端，或者退订所有(如果没有给出)

   * 格式：UNSUBSCRIBE channel [channel ...]

---

### 事务

 * 简介： Redis事务让一组命令在单个步骤中执行。事务中有两个属性，这说明如下：
        在一个事务中所有命令按顺序执行作为一个单一独立的操作。
        Redis事务也是原子的。原子就意味着要么所有命令都执行，要么都不进行处理

 * MULTI：

   * MULTI命令标志着一个事务块的开始。后续将使用EXEC命令执行排队等待原子

 * WATCH：

   * watch命令标记要关注事务给定键的条件执行

   * 格式：WATCH key [key ...]

 * UNWATCH：

   * UNWATCH命令为事务刷新所有以前关注过的键

 * EXEC：

   * EXEC命令执行所有先前排队在一个事务中的命令，并恢复连接状态正常

 * DISCARD ：

   * 发出命令后，MULTI后丢弃所有


