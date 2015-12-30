---
layout: post
title:  "Python Collection模块"
date:   2015-09-18 21:13:20
categories: Python
excerpt: Python Collection模块
---

* content
{:toc}


## 序

python 的Collection有一些处理列表、元组、字典的有趣方法，就在这里mark一下

---

### namedtuple

 * namedtuple是一个函数，它用来创建一个自定义的tuple对象，并且规定了tuple元素的个数，并可以用属性而不是索引来引用tuple的某个元素

 * 使用namedtuple可以很好的定义诸如坐标、圆之类的东西
 <pre><code>
    >>> from collections import namedtuple
    >>> Point = namedtuple('Point', ['x', 'y'])
    >>> p = Point(1, 2)
    >>> p.x
    1
    >>> p.y
    2
 </code></pre>

---

### deque

 * deque是为了高效实现插入和删除操作的双向列表，适合用于队列和栈

 * deque的使用方法如下
 <pre><code>
    >>> from collections import deque
    >>> q = deque(['a', 'b', 'c'])
    >>> q.append('x')
    >>> q.appendleft('y')
    >>> q
    deque(['y', 'a', 'b', 'c', 'x'])
 </code></pre>

---

### defaultdict

 * 使用dict时，如果引用的Key不存在，就会抛出KeyError。如果希望key不存在时，返回一个默认值，就可以用defaultdict
 <pre><code>
>>> from collections import defaultdict
>>> dd = defaultdict(lambda: 'N/A')
>>> dd['key1'] = 'abc'
>>> dd['key1'] # key1存在
'abc'
>>> dd['key2'] # key2不存在，返回默认值
'N/A'
 </code></pre>

---

### OrderedDict

 * 使用dict时，Key是无序的。在对dict做迭代时，我们无法确定Key的顺序
 <pre><code>
>>> from collections import OrderedDict
>>> d = dict([('a', 1), ('b', 2), ('c', 3)])
>>> d # dict的Key是无序的
{'a': 1, 'c': 3, 'b': 2}
 </code></pre>

 * 如果要保持Key的顺序，可以用OrderedDict
 <pre><code>
>>> from collections import OrderedDict
>>> od = OrderedDict([('a', 1), ('b', 2), ('c', 3)])
>>> od # OrderedDict的Key是有序的
OrderedDict([('a', 1), ('b', 2), ('c', 3)])
 </code></pre>

 * OrderedDict可以实现一个FIFO（先进先出）的dict，当容量超出限制时，先删除最早添加的Key

---

### Counter

 * Counter是一个简单的计数器，例如，统计字符出现的个数
counter[1,1,1,2,2,3,3,3,3,4,4,4,44]
