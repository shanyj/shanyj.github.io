---
layout: post
title:  "Elasticsearch 结构化查询&排序"
date:   2015-09-06 21:24:54
categories: Elasticsearch
excerpt: Elasticsearch 结构化查询&排序
---

* content
{:toc}


## 序

这里主要介绍下Elasticsearch 结构化查询和排序的一些知识

---

### 结构化查询

 * 结构化查询

    * 需要使用query参数
    <pre><code>
    GET /_search
    {
        "query": {
            "match": {
                "tweet": "elasticsearch"
            }
        }
    }
    </code></pre>
    <pre><code>
    GET /_search
    {
        "query": {
            "match_all": {}
        }
    }
    </code></pre>

 * 合并多子句
    <pre><code>
    {
        "bool": {
            "must":     { "match": { "tweet": "elasticsearch" }},
            "must_not": { "match": { "name":  "mary" }},
            "should":   { "match": { "tweet": "full text" }}
        }
    }
    </code></pre>

 * 查询过滤语句

    * term 过滤
    <pre><code>
    { "term": { "age":    26           }}
    </code></pre>

    * terms 过滤
    <pre><code>
    {
        "terms": {
            "tag": [ "search", "full_text", "nosql" ]
            }
    }
    </code></pre>

    * range 过滤
    <pre><code>
    {
    "range": {
        "age": {
            "gte":  20,
            "lt":   30
            }
        }
    }
    </code></pre>
    gt :: 大于,
    gte:: 大于等于,
    lt :: 小于,
    lte:: 小于等于

    * exists
    <pre><code>
    {
        "exists":   {
            "field":    "title"
        }
    }
    </code></pre>

 * multi_match查询
    <pre><code>
    {
        "multi_match": {
            "query":    "full text search",
            "fields":   [ "title", "body" ]
        }
    }
    </code></pre>
    multi_match查询允许你做match查询的基础上同时搜索多个字段

 * 短语搜索
    <pre><code>
    {
    "query" : { "match_phrase" : {
    "about" : "rock climbing" }
    } }
    </code></pre>

 * 查询与过滤条件的合并
    <pre><code>
    GET /_search
    {
        "query": {
            "filtered": {
                "query":  { "match": { "email": "business opportunity" }},
                "filter": { "term": { "folder": "inbox" }}
            }
        }
    }
    </code></pre>

 * 验证查询
    <pre><code>
    GET /gb/tweet/_validate/query
    {
       "query": {
          "tweet" : {
             "match" : "really powerful"
          }
       }
    }
    </code></pre>


---

### 排序

 * 字段值排序
    <pre><code>
    GET /_search
    {
        "query" : {
            "filtered" : {
                "filter" : { "term" : { "user_id" : 1 }}
            }
        },
        "sort": { "date": { "order": "desc" }}
    }
    </code></pre>

 * 多级排序

    * 结果集会先用第一排序字段来排序，当用用作第一字段排序的值相同的时候， 然后再用第二字段对第一排序值相同的文档进行排序，以此类推
    <pre><code>
    GET /_search
    {
        "query" : {
            "filtered" : {
                "query":   { "match": { "tweet": "manage text search" }},
                "filter" : { "term" : { "user_id" : 2 }}
            }
        },
        "sort": [
            { "date":   { "order": "desc" }},
            { "_score": { "order": "desc" }}
        ]
    }
    </code></pre>

 * 多值字段字符串排序

    * 为了使一个string字段可以进行排序，它必须只包含一个词：即完整的not_analyzed字符串。
    当然我们需要对字段进行全文本搜索的时候还必须使用被 analyzed 标记的字段

    * 我们想要的是同一个字段中同时包含这两种索引方式，我们只需要改变索引(index)的mapping即可。
    方法是在所有核心字段类型上，使用通用参数 fields对mapping进行修改
    <pre><code>
    "tweet": {
        "type":     "string",
        "analyzer": "english",
        "fields": {
            "raw": {
                "type":  "string",
                "index": "not_analyzed"
            }
        }
    }
    </code></pre>
    tweet 字段用于全文本的 analyzed 索引方式不变。
    新增的 tweet.raw 子字段索引方式是 not_analyzed

    * 现在，在给数据重建索引后，我们既可以使用 tweet 字段进行全文本搜索，也可以用tweet.raw字段进行排序
    <pre><code>
    GET /_search
    {
        "query": {
            "match": {
                "tweet": "elasticsearch"
            }
        },
        "sort": "tweet.raw"
    }
    </code></pre>

 * 相关性

    * 检索词频率：检索词在该字段出现的频率？出现频率越高，相关性也越高

    * 反向文档频率：每个检索词在索引中出现的频率，频率越高，相关性越低

    * 字段长度准则：字段的长度是多少？长度越长，相关性越低

 * Explain Api

    * 当explain选项加到某一文档上时，它会告诉你为何这个文档会被匹配，以及一个文档为何没有被匹配
    <pre><code>
    GET /_search
    GET /us/tweet/12/_explain
    {
       "query" : {
          "filtered" : {
             "filter" : { "term" :  { "user_id" : 2           }},
             "query" :  { "match" : { "tweet" :   "honeymoon" }}
          }
       }
    }
    </code></pre>