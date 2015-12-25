---
layout: post
title:  "Redis随笔（11）-Redis 链接&基准&管道&淘汰"
date:   2015-07-21 20:54:19
categories: Redis
excerpt: Redis随笔（11）-Redis 链接&基准&管道&淘汰
---

* content
{:toc}


## 序

主要记录了Redis的链接&基准&淘汰的一些命令

---

### 链接

 * Redis的连接命令基本上都是用于管理Redis的服务器与客户端连接

 * AUTH:

   * 用来向服务器验证给定的密码。 如果密码与在配置文件中的口令相匹配，则服务器会返回OK状态码，并开始接受命令。否则，将返回一个错误，并且客户需要尝试新的密码

   * 格式：AUTH PASSWORD

 * ECHO:

   * 打印指定的字符串

   * 格式：ECHO SAMPLE_STRING

 * PING:

   * 查是否服务器正在运行

 * SELECT:

   * 用来指定的基于零的数值索引选择的数据块。新的连接始终使用DB 0

   * 格式：SELECT DB_INDEX

 * QUIT:

   * 要求服务器关闭连接。该连接只要所有未完成操作写入到客户端后就关闭

---

### Redis基准

 * 格式：redis-benchmark [option] [option value]

    * -h	指定服务器的主机名	127.0.0.1

    * -p	指定服务器端口	6379

    * -c	指定并行连接数	50

    * -n	指定请求总数	10000

    * -q	Redis强制安静操作。只显示查询/秒值

    * -t	只有运行的逗号分隔的测试列表。

    * 示例：redis-benchmark -h 127.0.0.1 -p 6379 -t set,lpush -n 100000 -q

---

### 管道

 * Redis是一个TCP服务器，并支持请求/响应协议。redis的一个请求完成需要下面的步骤：

   * 客户端发送一个查询到服务器，并从套接字中读取，通常在封闭的方式，对服务器的响应

   * 服务器处理命令并将响应返回给客户端

 * Redis 管道技术可以在服务端未响应时，客户端可以继续向服务端发送请求，并最终一次性读取所有服务端的响应

---

### 淘汰机制

 * 修改配置文件中的maxmemory参数，限制redis可以使用的内存，当超出时按照maxmemory-policy策略来进行删除

   * volatile-lru  使用lru删除一个有生存时间的键

   * allkeys-lru   使用lru删除一个键

   * volatile-random  删除随机一个有生存时间的键

   * allkeys-random    删除随机一个键

   * volatile-ttl   删除沈村时间最近的一个键盘

   * noeviction  不删除，只返回错误

