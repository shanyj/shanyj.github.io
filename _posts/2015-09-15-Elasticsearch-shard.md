---
layout: post
title:  "Elasticsearch 段&结构化搜索"
date:   2015-09-15 20:18:47
categories: Elasticsearch
excerpt: Elasticsearch 段&结构化搜索
---

* content
{:toc}


## 序

这里主要介绍下Elasticsearch 段和一些搜索的一些知识

---

### 段

 * 使文档可被搜索

    * 在全文检索的早些时候，会为整个文档集合建立一个大索引，并且写入磁盘。只有新的索引准备好了，它就会替代旧的索引，最近的修改才可以被检索

    * 不可变的索引有它的缺点，首先是它不可变！你不能改变它。如果想要搜索一个新文档，必须重见整个索引。
    这不仅严重限制了一个索引所能装下的数据，还有一个索引可以被更新的频次

 * 动态索引

    * 一个段(segment)是有完整功能的倒排索引
    索引指的是段的集合，再加上提交点(commit point，包括所有段的文件)。新的文档，在被写入磁盘的段之前，首先写入内存区的索引缓存

    * 一个per-segment search如下工作:
        新的文档首先写入内存区的索引缓存。
        不时，这些buffer被提交。
        一个新的段——额外的倒排索引——写入磁盘。
        新的提交点写入磁盘，包括新段的名称。
        磁盘是fsync’ed(文件同步)——所有写操作等待文件系统缓存同步到磁盘，确保它们可以被物理写入。
        新段被打开，它包含的文档可以被检索。
        内存的缓存被清除，等待接受新的文档。

    * 段是不可变的，所以文档既不能从旧的段中移除，旧的段也不能更新以反映文档最新的版本。
    相反，每一个提交点包括一个.del文件，包含了段上已经被删除的文档。
    当一个文档被删除，它实际上只是在.del文件中被标记为删除，依然可以匹配查询，但是最终返回之前会被从结果中删除

    * 文档的更新操作是类似的：当一个文档被更新，旧版本的文档被标记为删除，新版本的文档在新的段中索引。
    也许该文档的不同版本都会匹配一个查询，但是更老版本会从结果中删除

 * 近实时搜索

    * 磁盘是瓶颈。提交一个新的段到磁盘需要fsync操作，确保段被物理地写入磁盘，即时电源失效也不会丢失数据。
    但是fsync是昂贵的，它不能在每个文档被索引的时就触发

    * 但是新的段首先写入文件系统缓存，这代价很低，之后会被同步到磁盘，这个代价很大。
    但是一旦一个文件被缓存，它也可以被打开和读取，就像其他文件一样

    * refeash API:在Elesticsearch中，这种写入打开一个新段的轻量级过程，叫做refresh。默认情况下，每个分片每秒自动刷新一次。
        这就是为什么说Elasticsearch是近实时的搜索了：文档的改动不会立即被搜索，但是会在一秒内可见。
        这会困扰新用户：他们索引了个文档，尝试搜索它，但是搜不到。解决办法就是执行一次手动刷新POST /_refresh

    * 你可以通过修改配置项refresh_interval减少刷新的频率
    <pre><code>
    PUT /my_logs
    {
      "settings": {
        "refresh_interval": "30s" <1>
      }
    }
    </code></pre>

 * 持久化变更

    * 一次全提交同步段到磁盘，写提交点，这会列出所有的已知的段,ES使用这次提交点决定哪些段属于当前的分片
    ES增加了事务日志（translog），来记录每次操作

    * 当一个文档被索引，它被加入到内存缓存，同时加到事务日志。

    * refresh使得分片的进入如下描述的状态。每秒分片都进行refeash：
            内存缓冲区的文档写入到段中，但没有fsync。
            段被打开，使得新的文档可以搜索。
            缓存被清除

    * 随着更多的文档加入到缓存区，写入日志，这个过程会继续

    * 不时地，比如日志很大了，新的日志会创建，会进行一次全提交
            内存缓存区的所有文档会写入到新段中。
            清除缓存
            一个提交点写入硬盘
            文件系统缓存通过fsync操作flush到硬盘
            事务日志被清除

    * 事务日志记录了没有flush到硬盘的所有操作。当故障重启后，ES会用最近一次提交点从硬盘恢复所有已知的段，并且从日志里恢复所有的操作。
    分片每30分钟，或事务日志过大会进行一次flush操作

 * 合并段

    * 有太多的段是一个问题。每个段消费文件句柄，内存，cpu资源。更重要的是，每次搜索请求都需要依次检查每个段。段越多，查询越慢。
    ES通过后台合并段解决这个问题。小段被合并成大段，再合并成更大的段

    * 旧的文档从文件系统删除的时候。旧的段不会再复制到更大的新段中

    * optimize API:opttimize API最好描述为强制合并段API。它强制分片合并段以达到指定max_num_segments参数。
    这是为了减少段的数量（通常为1）达到提高搜索性能的目的。
    POST /logstash-2014-10/_optimize?max_num_segments=1

---

### 结构化搜索

 * 缓存:

    * 一旦缓存后，当遇到相同的过滤时，这些字节集就可以被重用，而不需要重新运算整个过滤

    * 缓存的字节集很“聪明”：他们会增量更新。你索引中添加了新的文档，只有这些新文档需要被添加到已存的字节集中，而不是一遍遍重新计算整个缓存的过滤器

    * 禁用缓存:
    { "range" : { "timestamp" : { "gt" : "2014-01-02 16:15:14" }, "_cache": false } }

 * range 过滤器

    * range 过滤器支持_日期数学_操作。例如，我们想找到所有最近一个小时的文档
    <pre><code>
    {"range" : {
        "timestamp" : {
            "gt" : "now-1h"
        }
    }
    </code></pre>

    * 日期计算也能用于实际的日期，而不是仅仅是一个像 now 一样的占位符。只要在日期后加上双竖线，就能使用日期数学表达式了
    <pre><code>
    "range" : {
        "timestamp" : {
            "gt" : "2014-01-01 00:00:00",
            "lt" : "2014-01-01 00:00:00||+1M" <1>
        }
    }
    </code></pre>