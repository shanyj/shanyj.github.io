---
layout: post
title:  "Elasticsearch 脚本&冲突&集群"
date:   2015-08-28 20:41:07
categories: Elasticsearch
excerpt: Elasticsearch 脚本&冲突&集群
---

* content
{:toc}


## 序

这里主要介绍下Elasticsearch 脚本的使用，冲突处理规则和集群的一些知识

---

### 脚本

 * 使用脚本局部更新

   * 脚本能够使用 update API改变 _source 字段的内容,它在脚本内部以 ctx._source 表示
   <pre><code>
    POST /website/blog/1/_update
    {
       "script" : "ctx._source.tags+=new_tag",
       "params" : {
          "new_tag" : "search"
       }
    }
   </code></pre>

   * 更新可能不存在的文档
   <pre><code>
    POST /website/pageviews/1/_update
    {
       "script" : "ctx._source.views+=1",
       "upsert": {
           "views": 1
       }
    }
   </code></pre>

   * 更新和冲突: 为了避免丢失数据，update API在检索(retrieve)阶段检索文档的当前_version，然后在重建索引(reindex)阶段通过index请求提交。
    如果其他进程在检索(retrieve)和重加索引(reindex)阶段修改了文档，_version将不能被匹配，然后更新失败
   <pre><code>
    POST /website/pageviews/1/_update?retry_on_conflict=5 <1>
    {
       "script" : "ctx._source.views+=1",
       "upsert": {
           "views": 0
       }
    }
   </code></pre>

---

### 文档冲突处理（乐观并发控制）

 * 我们可以指定文档的version来做想要的更改。如果那个版本号不是现在的,我们的请求就失败了
   <pre><code>
    PUT /website/blog/1?version=1
    {"title": "My first blog entry",
    "text": "Starting to get the hang of this..."
    }
   </code></pre>

 * 上述代码只希望文档的 _version 是 1 时更新才生效。
    所有更新和删除文档的请求都接受 version 参数,它可以允许在你的代码中增加乐观锁控制。
    如果有冲突，我们需要重新检索最新文档然后申请新的更改操作

 * 如果主数据库有版本字段——或一些类似于 timestamp 等可以用于版本控制的字段——是你就可以在Elasticsea rch的查询字符串后面添加
    version_type=external 来使用这些版本号。版本号必须是整数,大于零小于 9.2e+1 8 。
    外部版本号与之前说的内部版本号在处理的时候有些不同。
    它不再检查 _version 是否与请求中指定的一致,而是 检查是否小于指定的版本。
    如果请求成功,外部版本号就会被存储到 _version 中

 * 创建一个包含外部版本号 5 的新博客,我们可以这样做:
    <pre><code>
    PUT /website/blog/2?version=5&version_type=external {
    "title": "My first external blog entry",
    "text": "Starting to get the hang of this..."
    }
    </code></pre>

 * 现在我们更新这个文档,指定一个新 version 号码为 10 :
    <pre><code>
    PUT /website/blog/2?version=10&version_type=external {
    "title": "My first external blog entry",
    "text": "This is a piece of cake..."
    }
    </code></pre>

---

### 集群内部工作方式

 * 空集群:一个节点(node)就是一个Elasticsearch实例，而一个集群(cluster)由一个或多个节点组成，
    它们具有相同的cluster.name，它们协同工作，分享数据和负载。当加入新的节点或者删除一个节点时，集群就会感知到并平衡数据

 * 集群健康:GET /_cluster/health

 * 添加索引:实际上，索引只是一个用来指向一个或多个分片(shards)的“逻辑命名空间(logical namespace)
    一个分片(shard)是一个最小级别“工作单元(worker unit)”,它只是保存了索引中所有数据的一部分。
    复制分片只是主分片的一个副本，它可以防止硬件故障导致的数据丢失，同时可以提供读请求，比如搜索或者从别的shard取回文档。
    当索引创建完成的时候，主分片的数量就固定了，但是复制分片的数量可以随时调整
    <pre><code>
    PUT /blogs
    {
       "settings" : {
          "number_of_shards" : 3,
          "number_of_replicas" : 1
       }
    }
    </code></pre>

 * 横向扩展:
    <pre><code>
     PUT /blogs/_settings
    {
       "number_of_replicas" : 2
    }
    </code></pre>