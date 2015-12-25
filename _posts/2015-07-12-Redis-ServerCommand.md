---
layout: post
title:  "Redis服务器命令"
date:   2015-07-12 20:31:22
categories: Redis
excerpt: Redis服务器命令
---

* content
{:toc}
fuqu

## 序

主要记录了redis服务器的相关命令，如下

---

### redis服务器命令

 * BGREWRITEAOF：

   * 指示Redis开始追加唯一的文件重写过程

 * BGSAVE：

   * 保存在数据库后台。该行代码立即返回。 Redis父进程继续服务客户，子进程在磁盘上节省数据库，然后退出

   * 相似命令：SAVE命令执行同步保存数据集产生的所有Redis实例中的数据的快照时间点，在关系数据库文件的形式

 * CLIENT KILL：

   * 关闭特定的客户端连接

   * 命令格式：

    CLIENT KILL ADDR ip:port. 这是完全一样旧的三个参数的行为

    CLIENT KILL ID client-id. 可以通过其独特的ID字段，这是从Redis2.8.12在启动客户端LIST命令杀死一个客户端

    CLIENT KILL TYPE 类型，其中类型是正常的，从站PubSub之一。这将关闭在指定的类中的所有客户端的连接

    CLIENT KILL SKIPME yes/no. 默认情况下，此选项设置为yes，也就是客户端调用的命令将不会杀死，但将此选项设置为无会也杀死了客户端调用命令的效果

 * CLIENT GETNAME：

   * 返回由客户SETNAME为设置当前连接的名称。因为每一个新的连接开始没有关联的名字，如果没有名字被分配返回一个空批量回复

   * 相似命令：CLIENT SETNAME 命令指定一个名称到当前连接,CLIENT SETNAME connection-name

   * 相似命令：CLIENT LIST 获取客户端连接到服务器的连接列表

 * CLIENT PAUSE：

   * 是一个连接控制命令可以暂停所有Redis客户指定的时间量(以毫秒为单位)。该命令将执行以下操作：
它会停止正常和发布/订阅的客户处理所有挂起的命令

   * 格式：CLIENT PAUSE timeout

 * COMMAND：

   * 数组答复Redis命令的详细信息

   * 相似命令：COMMAND COUNT返回该服务器上Redis命令总数量

   * 相似命令：COMMAND INFO命令返回关于Redis命令的详细信息。COMMAND INFO command-name


 * CONFIG GET：

   * 是用来读取运行Redis服务器的配置参数

   * 格式：CONFIG GET parameter

   * 相似命令：CONFIG Set,设置运行Redis服务器的配置参数,CONFIG Set parameter value

 * DBSIZE:

   * 用来得到所选数据库key数量

 * FLUSHALL:

   * 删除所有现有的数据库，而不仅仅是当前选择的一个的键。此命令不会失败

   * 相似命令：FLUSHDB命令删除当前选择的数据块的键。此命令不会失败

 * info:

   * 返回有关简单的格式

 * LASTSAVE:

   * 返回Unix时间的最后一个数据块保存成功执行

 * MONITOR:

   * 是调试命令流回来Redis服务器处理每一个指令。它可以有助于理解数据库发生了什么事情

 * SHUTDOWN:

   * 停止所有客户端，进行保存，清空所有追加仅文件(如果AOF启用)，并退出服务器

   * 格式：SHUTDOWN [NOSAVE] [SAVE]

 * SYNC:

   * 用于同步主站服务到从站










