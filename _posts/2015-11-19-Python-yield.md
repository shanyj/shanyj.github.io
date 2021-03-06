---
layout: post
title:  "Python 迭代器&生成器"
date:   2015-11-19 20:41:18
categories: Python
excerpt: Python 迭代器&生成器
---

* content
{:toc}


## 序

在这里记录一下python迭代器与生成器的一些基本概念和用法

---

### 迭代器

 * 迭代器是访问集合元素的一种方式。迭代器对象从集合的第一个元素开始访问，直到所有的元素被访问完结束

 * 迭代器只能往前不会后退,对于原生支持随机访问的数据结构（如tuple、list），迭代器和经典for循环的索引访问相比并无优势，反而丢失了索引值（可以使用内建函数enumerate()找回这个索引值）。但对于无法随机访问的数据结构（比如set）而言，迭代器是唯一的访问元素的方式。
 另外，迭代器的一大优点是不要求事先准备好整个迭代过程中所有的元素

 * 迭代器仅仅在迭代到某个元素时才计算该元素，而在这之前或之后，元素可以不存在或者被销毁。这个特点使得它特别适合用于遍历一些巨大的或是无限的集合，比如几个G的文件，或是斐波那契数列等等

 * 迭代器有两个基本的方法

   * next方法：返回迭代器的下一个元素

   * __iter__方法：返回迭代器对象本身

 * 例子：
 <pre><code>
 class Fab(object):
    def __init__(self, max):
        self.max = max
        self.n, self.a, self.b = 0, 0, 1

    def __iter__(self):
        return self

    def next(self):
        if self.n < self.max:
            r = self.b
            self.a, self.b = self.b, self.a + self.b
            self.n = self.n + 1
            return r
        raise StopIteration()
 print Fabs(5)
 </code></pre>

 * 使用next()方法可以访问下一个元素：
 <pre><code>
>>> it.next()
0
>>> it.next()
1
>>> it.next()
2
 </code></pre>

 * python处理迭代器越界是抛出StopIteration异常
 <pre><code>
lst = range(5)
it = iter(lst)
try:
    while True:
        val = it.next()
        print val
except StopIteration:
    pass
 </code></pre>

 * 事实上，因为迭代器如此普遍，python专门为for关键字做了迭代器的语法糖。在for循环中，Python将自动调用工厂函数iter()获得迭代器，自动调用next()获取元素，还完成了检查StopIteration异常的工作

---

### 生成器

 * 带有 yield 的函数在 Python 中被称之为 generator（生成器）

 * 简单地讲，yield 的作用就是把一个函数变成一个 generator，带有 yield 的函数不再是一个普通函数，Python 解释器会将其视为一个 generator，
 调用时不会执行函数，而是返回一个 iterable 对象！
 在 for 循环执行时，每次循环都会执行 fab 函数内部的代码，执行到 yield  时， 函数就返回一个迭代值，下次迭代时，
 代码从 yield 的下一条语句继续执行，而函数的本地变量看起来和上次中断执行前是完全一样的，
 于是函数继续执行，直到再次遇到 yield。看起来就好像一个函数在正常执行的过程中被 yield 中断了数次，每次中断都会通过 yield 返回当前的迭代值。

 * 例子：
 <pre><code>
  def read_file(fpath):
    BLOCK_SIZE = 1024
    with open(fpath, 'rb') as f:
        while True:
            block = f.read(BLOCK_SIZE)
            if block:
                yield block
            else:
                return
 </code></pre>

 * 如果直接对文件对象调用 read() 方法，会导致不可预测的内存占用。好的方法是利用固定长度的缓冲区来不断读取文件内容。通过 yield，我们不再需要编写读文件的迭代类，就可以轻松实现文件读取
