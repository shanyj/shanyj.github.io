---
layout: post
title:  "Redis随笔（9）-排序"
date:   2015-07-15 21:11:43
categories: Redis
excerpt: Redis随笔（9）-排序
---

* content
{:toc}


## 序

redis的一个强大之处就是排序，可以轻易解决传统数据库对数据排序组合的难题，下面是redis排序命令的一些总结

---

### SORT KEY

 * 这个是最简单的情况，没有任何选项就是简单的对集合自身元素排序并返回排序结果,sort命令可以对列表、集合、有序集合进行排序,对有序集合排序时会忽略分数

    <pre><code>
    redis> lpush ml 12
    (integer) 1
    redis> lpush ml 11
    (integer) 2
    redis> lpush ml 23
    (integer) 3
    redis> lpush ml 13
    (integer) 4
    redis> sort ml
    1. "11"
    2. "12"
    3. "13"
    4. "23"
    </code></pre>

---

### [ASC|DESC] [ALPHA]

 * sort默认的排序方式（asc）是从小到大排的,当然也可以按照逆序或者按字符顺序排。逆序可以加上desc选项，想按字母顺序排可以加alpha选项，当然alpha可以和desc一起用

    <pre><code>
    redis> lpush mylist baidu
    (integer) 1
    redis> lpush mylist hello
    (integer) 2
    redis> lpush mylist xhan
    (integer) 3
    redis> lpush mylist soso
    (integer) 4
    redis> sort mylist alpha
    1. "baidu"
    2. "hello"
    3. "soso"
    4. "xhan"
    redis> sort mylist desc alpha
    1. "xhan"
    2. "soso"
    3. "hello"
    4. "baidu"
    </code></pre>

---

### [BY pattern]

 * 除了可以按集合元素自身值排序外，还可以将集合元素内容按照给定pattern组合成新的key，并按照新key中对应的内容进行排序

 <pre><code>
    redis> set name11 nihao
    OK
    redis> set name12 wo
    OK
    redis> set name13 shi
    OK
    redis> set name23 lala
    OK
    redis> sort ml by name*
    1) "23"
    2) "11"
    3) "13"
    4) "12"
 </code></pre>

 * \*代表了ml中的具体元素值,当参考建不存在时默认为默认为0,当参考键相同时按照本身的顺序排序

 * 按照了name\*字符串中的字母排序m1进行排序

 * 还可以依照哈希中的某个值来进行排序

  <pre><code>
    hset user11 name 1
    ……
    sort ml by user*->name
 </code></pre>
 依照哈希中的name字段排序

---

### [GET pattern]

 * 我们也可以通过get选项去获取指定pattern作为新key对应的值

 <pre><code>
    redis> sort ml by name* get name*  alpha
    1. "lala"
    2. "nihao"
    3. "shi"
    4. "wo"
 </code></pre>

 * get选项可以有多个。by只能有一个,对应的不存在时候返回的是nil。可以没有by只有get

 * \#特殊符号引用的是原始集合也就是ml

  <pre><code>
    redis> sort ml by name* get name* get #  alpha
    1. "lala"
    2. "23"
    3. "nihao"
    4. "11"
    5. "shi"
    6. "13"
    7. "wo"
    8. "12"
 </code></pre>

---

### [LIMIT start count]

 * 上面例子返回结果都是全部。limit选项可以限定返回结果的数量

 <pre><code>
    redis> sort ml get name* limit 1 2
    1. "wo"
    2. "shi"
 </code></pre>

 * start下标是从0开始的，这里的limit选项意思是从第二个元素开始获取2个

---

### [STORE dstkey]

 * 如果对集合经常按照固定的模式去排序，那么把排序结果缓存起来会减少不少cpu开销.使用store选项可以将排序内容保存到指定key中

 <pre><code>
    redis> sort ml get name* limit 1 2 store cl
    (integer) 2
    redis> type cl
    list
    redis> lrange cl 0 -1
    1. "wo"
    2. "shi"
 </code></pre>

---

### 注意

 * 尽量减少待排序元素数量

 * 使用limit

 * 使用缓存缓存结果

