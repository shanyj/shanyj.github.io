---
layout: post
title:  "Elasticsearch 映射&分析"
date:   2015-09-02 20:53:32
categories: Elasticsearch
excerpt: Elasticsearch 映射&分析
---

* content
{:toc}


## 序

这里主要介绍下Elasticsearch 映射和分析的一些概念

---

### 映射和分析

 * 术语

    * 映射(mapping)机制用于进行字段类型确认，将每个字段匹配为一种确定的数据类型(string, number, booleans, date等)

    * 分析(analysis)机制用于进行全文文本(Full Text)的分词，以建立供搜索用的反向索引

 * 映射

    * 我们的数据在_all字段的索引方式和在date字段的索引方式不同

    * GET /gb/_mapping/tweet

    * Elasticsearch为对字段类型进行猜测，动态生成了字段和类型的映射关系

 * 全文文本(Full text)

    * 为了方便在全文文本字段中进行这些类型的查询，Elasticsearch首先对文本分析(analyzes)，然后使用结果建立一个倒排索引

 * 倒排索引

    * 倒排索引由在文档中出现的唯一的单词列表，以及对于每个单词在文档中的位置组成

 * 分析

    * 表征化和标准化的过程叫做分词(analysis)

    * 一个分析器(analyzer)只是一个包装用于将三个功能放到一个包里：字符过滤器、分词器、表征过滤

    * 当我们索引(index)一个文档，全文字段会被分析为单独的词来创建倒排索引

    * 当你查询全文(full text)字段，查询将使用相同的分析器来分析查询字符串，以产生正确的词列表

    * 当你查询一个确切值(exact value)字段，查询将不分析查询字符串，但是你可以自己指定

    * 例如:date字段包含一个确切值：单独的一个词"2014-09-15"。
    _all字段是一个全文字段，所以分析过程将日期转为三个词："2014"、"09"和"15"

 * 测试分析器

    * GET /_analyze?analyzer=standard

    * 返回值中token是一个实际被存储在索引中的词。position指明词在原文本中是第几个出现的。start_offset和end_offset表示词在原文本中占据的位置

    * 当Elasticsearch在你的文档中探测到一个新的字符串字段，它将自动设置它为全文string字段并用standard分析器分析

 * Elasticsearch字段类型

    * String : string

    * Whole number : byte, short, integer, long

    * Floating point : float, double

    * Boolean : boolean

    * Date : date

 * 对于string字段，两个最重要的映射参数是index和analyer

    * index参数控制字符串以何种方式被索引，包含以下三种:

    * analyzed	首先分析这个字符串，然后索引。换言之，以全文形式索引此字段。

    * not_analyzed	索引这个字段，使之可以被搜索，但是索引内容和指定值一样。不分析此字段。

    * no 不索引这个字段。这个字段不能为搜索到。

    * 其他简单类型——long、double、date等等——也接受index参数，但相应的值只能是no和not_analyzed，它们的值不能被分析

    * 使用analyzer参数来指定哪一种分析器将在搜索和索引的时候使用

    * 默认的，Elasticsearch使用standard分析器，但是你可以通过指定一个内建的分析器来更改它

 * 更新映射

    * 你可以在第一次创建索引的时候指定映射的类型。此外，你也可以晚些时候为新类型添加映射（或者为已有的类型更新映射）

    * 你可以向已有映射中增加字段，但你不能修改它。如果一个字段在映射中已经存在，这可能意味着那个字段的数据已经被索引。
    如果你改变了字段映射，那已经被索引的数据将错误并且不能被正确的搜索到

    * 在tweet的映射中增加一个新的not_analyzed类型的文本字段，叫做tag
    <pre><code>
    PUT /gb/_mapping/tweet
    {
      "properties" : {
        "tag" : {
          "type" :    "string",
          "index":    "not_analyzed"
        }
      }
    }
    </code></pre>

 * 复合字段

    * 数组中所有值必须为同一类型。不能把日期和字符串混合

    * 如果你创建一个新字段，这个字段索引了一个数组，Elasticsearch将使用第一个值的类型来确定这个新字段的类型

 * 空字段

    * 这四个字段将被识别为空字段而不被索引：

    * "empty_string":             "",

    * "null_value":               null,

    * "empty_array":              [],

    * "array_with_null_value":    [ null ]

 * Elasticsearch 会动态的检测新对象的字段，并且映射它们为 object 类型，将每个字段加到 properties 字段下

