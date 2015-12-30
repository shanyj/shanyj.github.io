---
layout: post
title:  "Elasticsearch 索引管理"
date:   2015-09-12 19:41:19
categories: Elasticsearch
excerpt: Elasticsearch 索引管理
---

* content
{:toc}


## 序

这里主要介绍下Elasticsearch 索引管理的一些知识

---

### 索引管理

 * 删除索引

    * DELETE /index_*     删除以index_开头的索引

    * 删除所有索引 DELETE /_all

 * 配置分析器

    * standard 分析器是用于全文字段的默认分析器,他包含:

    * • standard 分词器,在词层级上分割输入的文本。

    * • standard 表征过滤器,被设计用来整理分词器触发的所有表征。 • lowercase 表征过滤器,将所有表征转换为小写。

    * • stop 表征过滤器,删除所有可能会造成搜索歧义的停用词,如 a , the , and , is

    * 默认情况下,停用词过滤器是被禁用的。如需启用它,你可以通过建一个基于 standard 分析器的自定义分析器,并且设置 stopwords 参数
    <pre><code>
    {
      "settings": {
        "analysis": {
          "analyzer": {
            "es_std": {
              "type": "standard",
              "stopwords": "_spanish_"
            }
          }
        }
      }
    }
    </code></pre>

 * 自定义分析器

    * 分析器 是三个顺序执行的组件的结合(字符过滤器,分词器,表征过滤器)
    <pre><code>
    PUT /my_index
    {
      "settings": {
        "analysis": {
          "char_filter": {
            "&_to_and": {
              "type": "mapping",
              "mappings": [
                "&=> and "
              ]
            }
          },
          "filter": {
            "my_stopwords": {
              "type": "stop",
              "stopwords": [
                "the",
                "a"
              ]
            }
          },
          "analyzer": {
            "my_analyzer": {
              "type": "custom",
              "char_filter": [
                "html_strip",
                "&_to_and"
              ],
              "tokenizer": "standard",
              "filter": [
                "lowercase",
                "my_stopwords"
              ]
            }
          }
        }
      }
    }
    </code></pre>

    * 除非我们告诉 Elasticsearch 在哪里使用,否则分析器不会起作用。我们可以通过下面的映射将它应用在一个 string 类型的字段上
    <pre><code>
    PUT /my_index/_mapping/my_type
    {
      "properties": {
        "title": {
          "type": "string",
          "analyzer": "my_analyzer"
        }
      }
    }
    </code></pre>

 * 类型陷阱

    * log_en 表示英语版的博客, blog_es 表示西班牙语版的博客。
    两种类型 都有 title 字段,但是其中一种类型使用 english 分析器,另一种使用 spanish 分析器
    这样就不能单纯使用match
    只能在查询中明确包含各自的类型名
    <pre><code>
    {
      "query": {
        "multi_match": {
          "query": "The quick brown fox",
          "fields": [
            "blog_en.title",
            "blog_es.title"
          ]
        }
      }
    }
    </code></pre>

    * 为了保证你不会遇到这些冲突,建议在同一个索引的每一个类型中,确保用同样的方式映射同名的字段

 * 元数据

    * _source
    <pre><code>
        GET /_search
        {
          "query": {
            "match_all": {}
          },
          "_source": [
            "title",
            "created"
          ]
        }
    </code>></pre>

    * _all
    <pre><code>
    #如果你决定不再使用 _all 字段,你可以通过下面的映射禁用它
        PUT /my_index/_mapping/my_type
        {
          "my_type": {
            "_all": {
              "enabled": false
            }
          }
        }
    </code></pre>
    <pre><code>
    #可以配置 _all 字段使用的分析器
        {
          "my_type": {
            "_all": {
              "analyzer": "whitespace"
            }
          }
        }
    </code></pre>

 * 文档id

    * _id :文档的字符串 ID
    _type :文档的类型名
    _index :文档所在的索引
    _uid : _type 和 _id 连接成的 type#id

    * 默认情况下, _uid 是被保存(可取回)和索引(可搜索)的。
    _type 字段被索引但是没有保存, _id 和 _index 字段既没有索引也没有储存,它们并不是真实存在的

    * path 设置告诉 Elasticsearch 它需要从文档本身的哪个字段中生成 _id
    <pre><code>
    PUT /my_index
    {
      "mappings": {
        "my_type": {
          "_id": {
            "path": "doc_id"
          },
          "properties": {
            "doc_id": {
              "type": "string",
              "index": "not_analyzed"
            }
          }
        }
      }
    }
    </code></pre>

 * 动态映射

    * 当遭遇一个位置的字段时,它通过动态映射来确定字段的数据类型且自动将该字段加到类型映射中

    * 可以通过 dynamic 设置来控制这些行为,它接受下面几个选项:
    true :自动添加字段(默认),
    false :忽略字段,
    strict :当遇到未知字段时抛出异常
    <pre><code>
    {
      "mappings": {
        "my_type": {
          "dynamic": "strict",
          "properties": {
            "title": {
              "type": "string"
            },
            "stash": {
              "type": "object",
              "dynamic": true
            }
          }
        }
      }
    }
    </code></pre>

    * 将 dynamic 设置成 false 完全不会修改 _source 字段的内容。
    _source 将仍旧保持你索引时的完整 JSON 文档。然而,没有被添加到映射的未知字段将不可被搜索

 * 自定义动态索引

    * 日期检测:日期检测可以通过在根对象上设置 date_detection 为 false 来关闭
    <pre><code>
        PUT /my_index {
        "mappings": { "my_type": {
        "date_detection": false }
        } }
    </code></pre>
    使用这个映射,字符串将始终是 string 类型。假如你需要一个 date 字段,你得手动添加它

    * 重新索引数据:修改在已存在的数据最简单的方法是重新索引:建一个新配置好的索引,然后将所有的文档从旧的索引复制到新的上。
    为了更高效的索引旧索引中的文档,使用【scan-scoll】来批量读取旧索引的文档,然后将通过【bulk API】来将它们推送给新的索引

 * 索引别名

    * 索引别名就像一个快捷方式或软连接,可以指向一个或多个索引,PUT /my_index_v1/_alias/my_index

    * 你可以检测这个别名指向哪个索引:
    GET /*/_alias/my_index
    或哪些别名指向这个索引:
    GET /my_index_v1/_alias/*