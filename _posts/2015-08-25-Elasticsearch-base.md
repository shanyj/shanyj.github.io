---
layout: post
title:  "Elasticsearch 简介与文档基本命令"
date:   2015-08-25 21:13:45
categories: Elasticsearch
excerpt: Elasticsearch 简介与文档基本命令
---

* content
{:toc}


## 序

项目中使用elasticsearch来进行文件tag的搜索与存储,在这里就介绍下elasticsearch的基本原理和文档常用命令

---

### 简介

 * Elasticsearch是面向文档(document oriented)的,这意味着它可以存储整个对象或文档(document)。
然而它 不仅仅是存储,还会索引(index)每个文档的内容使之可以被搜索。在Elasticsearch中,你
可以对文档(而非成 行成列的数据)进行索引、搜索、排序、过滤。这种理解数据的方式与以往完全不同,
这也是Elasticsearch能够 执行复杂的全文搜索的原因之一。

 * 运行:bin目录下运行./elasticsearch,需要启动head页面时需要现进行安装plugin install mobz/elasticsearch-head

---

### 概念描述

 * 节点(node):是一个运行着的Elasticsearch实例

 * 集群(cluster):是一组具有相同 cluster.name 的节点集合,他们 协同工作,共享数据并提供故障转移和扩展功能,当然一个节点也可以组成一个集群

 * Elasticsearch集群可以包含多个索引(indices)(数据库),每一个索引可以包含多个类型(types)(表),
    每一 个类型包含多个文档(documents)(行),然后每个文档包含多个字段(Fields)(列)

 * 每个Index（对应Database）包含多个Shard，默认是5个，分散在不同的Node上，但不会存在两个相同的Shard（相同的主和复制）存在一个Node上。
    当索引创建完成的时候,主分片的数量就固定了,但是复制分片的数量可以随时调整

 * 数据被存储和索引在分片(shards)中,索引只是一个把一个或多个分片分组在一起的逻辑空 间。
    然而,这只是一些内部细节——我们的程序完全不用关心分片。对于我们的程序而言,文档存储在索引(index)中。
    剩下的细节由Elasticsearch关心既可

---

### 文档基础命令

 * 文档元数据:

    * _index	文档存储的地方

    * _type	文档代表的对象的类

    * _id	文档的唯一标识

 * 创建文档:

    * 指定id(如果存在会更新)：
    <pre><code>
    PUT /{index}/{type}/{id}
    {
      "field": "value",
    }
    </code></pre>

    * 自增id：
    <pre><code>
    POST /website/blog/
    {
      "title": "My second blog entry",
    }
    </code></pre>

 * 获取文档:

    * GET /website/blog/123?pretty

    * 检索文档的一部分:
    GET /website/blog/123?_source=title,text

    * GET /website/blog/123/_source
    则返回值只有_source

 * 防止覆盖:
     <pre><code>
     PUT /website/blog/123/_create
    { ... }
    </code></pre>

 * 删除:

    * DELETE /website/blog/123

    *  Elasticsearch将返回200 OK状态码和响应体。注意_version数字已经增加了
    当不存在时，"found"的值是false——_version依旧增加了。这是内部记录的一部分，它确保在多节点间不同操作可以有正确的顺序

 * 局部更新:

    * update API处理相同的检索-修改-重建索引流程
    <pre><code>
    POST /website/blog/1/_update
    {
       "doc" : {
          "tags" : [ "testing" ],
          "views": 0
       }
    }
    </code></pre>

 * 检索多个文档:

    * mget API参数是一个docs数组，数组的每个节点定义一个文档的_index、_type、_id元数据。
    如果你只想检索一个或几个确定的字段，也可以定义一个_source参数
    <pre><code>
    GET /_mget
    {
       "docs" : [
          {
             "_index" : "website",
             "_type" :  "blog",
             "_id" :    2
          },
          {
             "_index" : "website",
             "_type" :  "pageviews",
             "_id" :    1,
             "_source": "views"
          }
       ]
    }
    </code></pre>

    * 如果你想检索的文档在同一个_index中（甚至在同一个_type中），你就可以在URL中定义一个默认的/_index或者/_index/_type
    <pre><code>
    GET /website/blog/_mget
    {
       "docs" : [
          { "_id" : 2 },
          { "_type" : "pageviews", "_id" :   1 }
       ]
    }
    </code></pre>

    * 如果所有文档具有相同_index和_type，你可以通过简单的ids数组来代替完整的docs数组
    <pre><code>
    GET /website/blog/_mget
    {
       "ids" : [ "2", "1" ]
    }
    </code></pre>

    * 尽管前面提到有一个文档没有被找到，但HTTP请求状态码还是200。
    事实上，就算所有文档都找不到，请求也还是返回200，原因是mget请求本身成功了。
    如果想知道每个文档是否都成功了，你需要检查found标志。

 * 批量操作:

    * create:当文档不存在时创建之。index:创建新文档或替换已有文档。update：局部更新文档。delete：删除一个文档。

    * 每行必须以"\n"符号结尾，包括最后一行。这些都是作为每行有效的分离而做的标记。
    <pre><code>
    POST /_bulk
    { "delete": { "_index": "website", "_type": "blog", "_id": "123" }}
    { "create": { "_index": "website", "_type": "blog", "_id": "123" }}
    { "title":    "My first blog post" }
    { "index":  { "_index": "website", "_type": "blog" }}
    { "title":    "My second blog post" }
    { "update": { "_index": "website", "_type": "blog", "_id": "123", "_retry_on_conflict" : 3} }
    { "doc" : {"title" : "My updated blog post"} }
    </code></pre>