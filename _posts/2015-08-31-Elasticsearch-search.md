---
layout: post
title:  "Elasticsearch 搜索&分布式"
date:   2015-08-31 21:42:27
categories: Elasticsearch
excerpt: Elasticsearch 搜索&分布式
---

* content
{:toc}


## 序

这里主要介绍下Elasticsearch 搜索的一些基础只是和分布式

---

### 搜索

 * 术语

   * 映射(Mapping):数据在每个字段中的解释说明。分析(Analysis)	：全文是如何处理的可以被搜索的。领域特定语言查询(Query DSL)：Elasticsearch使用的灵活的、强大的查询语言

 * 空搜索:GET /_search

   * 返回值中hits数组中的每个结果都包含_index、_type和文档的_id字段，被加入到_source字段中这意味着在搜索结果中我们将可以直接使用全部文档。
    每个节点都有一个_score字段，这是相关性得分(relevance score)，它衡量了文档与查询的匹配程度

   * took告诉我们整个搜索请求花费的毫秒数

   * time_out值告诉我们查询超时与否

   * 需要注意的是timeout不会停止执行查询，它仅仅告诉你目前顺利返回结果的节点然后关闭连接。在后台，其他分片可能依旧执行查询，尽管结果已经被发送

 * 多索引和多类别:

    * /_search : 在所有索引的所有类型中搜索

    * /gb/_search : 在索引gb的所有类型中搜索

    * /gb,us/_search : 在索引gb和us的所有类型中搜索

    * /g*,u*/_search : 在以g或u开头的索引的所有类型中搜索

    * /gb/user/_search : 在索引gb的类型user中搜索

    * /gb,us/user,tweet/_search : 在索引gb和us的类型为user和tweet中搜索

    * /_all/user,tweet/_search : 在所有索引的user和tweet中搜索

 * 分页

    * size: 果数，默认10

    * from: 跳过开始的结果数，默认0

 * _all字段:

    * 当你索引一个文档，Elasticsearch把所有字符串字段值连接起来放在一个大字符串中，它被索引为一个特殊的字段_all

 * 高亮:
     <pre><code>
     "highlight": {
    "fields" : { "about" : {}
    } }
    </code></pre>

---

### 分布式

 * 确定所在分片:

   * shard = hash(routing) % number_of_primary_shards

   * 余数的范围永远是0到number_of_primary_shards - 1，这个数字就是特定文档所在的分片。
    这也解释了为什么主分片的数量只能在创建索引时定义且不能修改：如果主分片的数量在未来改变了，所有先前的路由值就失效了，文档也就永远找不到了

 * 过程:

   * 新建、索引或删除一个文档:客户端给Node 1发送新建、索引或删除请求。
    节点使用文档的_id确定文档属于分片0。它转发请求到Node 3，分片0位于这个节点上。
    Node 3在主分片上执行请求，如果成功，它转发请求到相应的位于Node 1和Node
    2的复制节点上。当所有的复制节点报告成功，Node 3报告成功到请求的节点，请求的节点再报告给客户端

   * 检索文档:客户端给Node 1发送get请求。
    节点使用文档的_id确定文档属于分片0。分片0对应的复制分片在三个节点上都有。此时，它转发请求到Node 2。
    Node 2返回endangered给Node 1然后返回给客户端。
    对于读请求，为了平衡负载，请求节点会为每个请求选择不同的分片——它会循环所有分片副本

   * 局部更新文档:客户端给Node 1发送更新请求。
    它转发请求到主分片所在节点Node 3。
    Node 3从主分片检索出文档，修改_source字段的JSON，然后在主分片上重建索引。
    如果有其他进程修改了文档，它以retry_on_conflict设置的次数重复步骤3，都未成功则放弃。
    如果Node 3成功更新文档，它同时转发文档的新版本到Node 1和Node 2上的复制节点以重建索引。
    当所有复制节点报告成功，Node 3返回成功给请求节点，然后返回给客户端

 * 参数设置:

   * replication:
    复制默认的值是sync。这将导致主分片得到复制分片的成功响应后才返回。
    如果你设置replication为async，请求在主分片上被执行后就会返回给客户端。它依旧会转发请求给复制节点，但你将不知道复制节点成功与否。

   * timeout:
    100表示100毫秒，30s表示30秒。

 * 奇怪的格式:为什么bulk API需要带换行符的奇怪格式，而不是像mget API一样使用JSON数组？
    批量中每个引用的文档属于不同的主分片，每个分片可能被分布于集群中的某个节点上。
    这意味着批量中的每个操作(action)需要被转发到对应的分片和节点上。
    Elasticsearch则是从网络缓冲区中一行一行的直接读取数据。它使用换行符识别和解析action/metadata行，以决定哪些分片来处理这个请求。
    这些行请求直接转发到对应的分片上。这些没有冗余复制，没有多余的数据结构。整个请求过程使用最小的内存在进行