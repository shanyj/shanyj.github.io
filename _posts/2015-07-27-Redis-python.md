---
layout: post
title:  "Python Redis模块"
date:   2015-07-27 21:18:28
categories: Redis Python
excerpt: Python Redis模块
---

* content
{:toc}


## 序

在python中调用redis十分easy，因为几乎大部分命令都与原生redis命令类似，但也有诸如pipeline和订阅等命令需要着重关注

---

### 安装与连接

 * 安装
<pre><code>
pip install redis
</code></pre>

 * 连接
<pre><code>
#直接链接
>>> import redis
>>> r = redis.StrictRedis(host='localhost', port=6379, db=0)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
'bar'

#使用链接池
>>> pool = redis.ConnectionPool(host='localhost', port=6379, db=0)
>>> r = redis.Redis(connection_pool=pool)

#使用linux套接字
>>> r = redis.Redis(unix_socket_path='/tmp/redis.sock')
</code></pre>

---

### 方法

 * 除了管道与订阅的大部分方法与redis命令相同，但有以下几点例外
   * DEL：’del’ 是 Python 语法的保留关键字。因此redis-py 使用 “delete” 代替

   * CONFIG GET|SET：分别用 config_get 和 config_set 实现

   * MULTI/EXEC：作为 Pipeline 类的一部分来实现。Pipeline执行时默认包含了MULTI 和 EXEC 语句。设置transaction=False可以关闭该功能。参见下面 Pipeline 部分

   * SUBSCRIBE/LISTEN: 和 pipeline 类似，但PubSub 作为单独的类实现，因为它需要保持连接状态，此时不能执行非PubSub命令。
调用 Redis 客户端的 pubsub 方法会返回实例，从而订阅频道和侦听消息。只能从Redis 客户端调用PUBLISH

   * ZADD：实现时 score 和 value 的顺序不小心弄反了，后来有人用了，就这样了

   * SETEX: time 和 value 的对换

---

### 管道

 * 默认是原子性的，包含了MULTI 和 EXEC 语句，要想解除的话
 <pre><code>
  pipe = r.pipeline(transaction=False)
  </code></pre>

 * 示例：
 <pre><code>
>>> r = redis.Redis(...)
>>> r.set('bing', 'baz')
>>> pipe = r.pipeline()
>>> pipe.set('foo', 'bar')
>>> pipe.get('bing')
>>> pipe.execute()
[True, 'baz']
  </code></pre>

 * 包含事务：
 <pre><code>
>>> with r.pipeline() as pipe:
...     while 1:
...         try:
...             pipe.watch('OUR-SEQUENCE-KEY')
...             current_value = pipe.get('OUR-SEQUENCE-KEY')
...             next_value = int(current_value) + 1

...             pipe.multi()
...             pipe.set('OUR-SEQUENCE-KEY', next_value)
...             pipe.execute()

...             break
  </code></pre>

---

###发布和订阅

 * redis发布 pubsub订阅
 <pre><code>
>>> r = redis.StrictRedis(...)
>>> p = r.pubsub()

>>> p.subscribe('my-first-channel', 'my-second-channel', ...)
>>> p.psubscribe('my-*', ...)

>>> r.publish('my-first-channel', 'some data')
2
>>> p.get_message()
{'channel': 'my-first-channel', 'data': 'some data', 'pattern': None, 'type': 'message'}
>>> p.get_message()
{'channel': 'my-first-channel', 'data': 'some data', 'pattern': 'my-*', 'type': 'pmessage'}
  </code></pre>

 * 持续监听:

   * 想要持续监听有两种方法,listen方法或者while循环

   * listen方法:
    <pre><code>
for message in p.listen():
    print message
  </code></pre>

   * while循环:
       <pre><code>
>>> while True:
>>>     message = p.get_message()
>>>     if message:
>>>         # do something with the message
>>>     time.sleep(0.001)  # be nice to the system :)
  </code></pre>
