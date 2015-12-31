---
layout: post
title:  "Elasticsearch 分布式搜索&聚合"
date:   2015-09-08 20:01:23
categories: Elasticsearch
excerpt: Elasticsearch 分布式搜索&聚合
---

* content
{:toc}


## 序

这里主要介绍下Elasticsearch 分布式搜索和聚合的一些知识

---

### 分布式搜索

 * 过程

    * 在搜索（search）API返回一页结果前，来自多个分片的结果必须被组合放到一个有序列表中。
    因此，搜索的执行过程分两个阶段，称为查询然后取回（query then fetch）

 * 查询阶段

    * 在初始化查询阶段（query phase），查询被向索引中的每个分片副本（原本或副本）广播。
    每个分片在本地执行搜索并且建立了匹配document的优先队列（priority queue）。
    一个优先队列（priority queue is）只是一个存有前n个（top-n）匹配document的有序列表。
    这个优先队列的大小由分页参数from＋size决定,查询阶段包含以下三步

    * 客户端发送一个search（搜索）请求给Node 3,Node 3创建了一个长度为from+size的空优先级队列

    * Node 3 转发这个搜索请求到索引中每个分片的原本或副本。每个分片在本地执行这个查询并且结果将结果到一个大小为from+size的有序本地优先队列里去

    * 每个分片返回document的ID和它优先队列里的所有document的排序值给协调节点Node 3。Node 3把这些值合并到自己的优先队列里产生全局排序结果

    * 每一个分片在本地执行查询和建立一个长度为from+size的有序优先队列——这个长度意味着它自己的结果数量就足够满足全局的请求要求。
    分片返回一个轻量级的结果列表给协调节点。只包含documentID值和排序需要用到的值，例如_score

 * 取回阶段

    * 分发阶段由以下步骤构成

    * 协调节点辨别出哪个document需要取回，并且向相关分片发出GET请求

    * 每个分片加载document并且根据需要丰富（enrich）它们，然后再将document返回协调节点

    * 一旦所有的document都被取回，协调节点会将结果返回给客户端

    * 协调节点先决定哪些document是实际（actually）需要取回的。例如，我们指定查询{ "from": 90, "size": 10 }，
    那么前90条将会被丢弃，只有之后的10条会需要取回。
    这些document可能来自与原始查询请求相关的某个、某些或者全部分片

 * 深分页

    * 查询然后取回过程虽然支持通过使用from和size参数进行分页，但是要在有限范围内（within limited）。
    还记得每个分片必须构造一个长度为from+size的优先队列吧，所有这些都要传回协调节点。
    这意味着协调节点要通过对分片数量 * (from + size)个document进行排序来找到正确的size个document

 * 搜索选项

    * preference:
        preference参数允许你控制使用哪个分片或节点来处理搜索请求,她接受如下一些参数,_primary， _primary_first， _local， _only_node:xyz， _prefer_node:xyz和_shards:2,3。

    * 结果震荡：
        用户每次刷新页面，结果顺序会发生变化。避免这个问题方法是对于同一个用户总是使用同一个分片。
        方法就是使用一个随机字符串例如用户的会话ID（session ID）来设置preference参数。

    * timeout
        timeout参数告诉协调节点最多等待多久，就可以放弃等待而将已有结果返回。返回部分结果总比什么都没有好

    * routing
        在搜索时，你可以指定一个或多个routing 值来限制只搜索那些分片而不是搜索index里的全部分片：
        GET /_search?routing=user_1,user2

    * search_type
        虽然query_then_fetch是默认的搜索类型，但也可以根据特定目的指定其它的搜索类型，例如：
        GET /_search?search_type=count
        count（计数）,
        query_and_fetch（查询并且取回）,
        scan（扫描）,scan（扫描）搜索类型是和scroll（滚屏）API连在一起使用的，可以高效地取回巨大数量的结果。它是通过禁用排序来实现的

 * 扫描和滚屏

    * scan（扫描）搜索类型是和scroll（滚屏）API一起使用来从Elasticsearch里高效地取回巨大数量的结果而不需要付出深分页的代价

    * scroll（滚屏）:
    一个滚屏搜索允许我们做一个初始阶段搜索并且持续批量从Elasticsearch里拉取结果直到没有结果剩下。这有点像传统数据库里的cursors（游标）。
    滚屏搜索会及时制作快照。这个快照不会包含任何在初始阶段搜索请求后对index做的修改。
    它通过将旧的数据文件保存在手边，所以可以保护index的样子看起来像搜索开始时的样子

    * scan（扫描）
    深度分页代价最高的部分是对结果的全局排序，但如果禁用排序，就能以很低的代价获得全部返回结果。
    扫描模式让Elasticsearch不排序，只要分片里还有结果可以返回，就返回一批结果
    <pre><code>
    GET /old_index/_search?search_type=scan&scroll=1m
    {
        "query": { "match_all": {}},
        "size":  1000
    }
    </code></pre>

---

### 聚合

 * 聚合
 <pre><code>
     {
      "query": {
        "match": {
          "last_name": "smith"
        }
      },
      "aggs": {
        "all_interests": {
          "terms": {
            "field": "interests"
          }
        }
      }
    }
 </code></pre>