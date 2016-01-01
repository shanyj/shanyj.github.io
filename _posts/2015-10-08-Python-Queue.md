---
layout: post
title:  "Python Queue模块"
date:   2015-10-08 20:21:12
categories: Python
excerpt: Python Queue模块
---

* content
{:toc}


## 序

Queue类
是一个队列的同步实现,
队列长度可为无限或者有限,下面就mark一下具体的使用方法

---

### 三种队列

 * queue模块有三种队列

    * python queue模块的FIFO队列先进先出。Queue.Queue(maxsize)

    * LIFO类似于堆。即先进后出。Queue.LifoQueue(maxsize)

    * 还有一种是优先级队列级别越低越先出来。Queue.PriorityQueue(maxsize)

---

### 队列存取

 * q.put(10)：
调用队列对象的put()方法在队尾插入一个项目。put()有两个参数，第一个item为必需的，为插入项目的值；第二个block为可选参数，默认为
1。如果队列当前为满且block为1，put()方法就使调用线程暂停,直到空出一个数据单元。如果block为0，put方法将引发Full异常

 * q.get()：
调用队列对象的get()方法从队头删除并返回一个项目。可选参数为block，默认为True。如果队列为空且block为True，get()就使调用线程暂停，直至有项目可用。
如果队列为空且block为False，队列将引发Empty异常

---

### 其他方法

 * Queue.qsize() : 返回队列的大小

 * Queue.empty() : 如果队列为空,返回True,反之False

 * Queue.full() : 如果队列满了,返回True,反之False

 * Queue.get_nowait() : 相当Queue.get(False)

 * Queue.put_nowait(item) : 相当Queue.put(item, False)

 * Queue.task_done() : 在完成一项工作之后,Queue.task_done() 函数向任务已经完成的队列发送一个信号Queue.join() 实际上意味着等到队列为空,再执行别的操作.

 * Queue.join() : 保持阻塞状态，直到处理了队列中的所有项目为止。在将一个项目添加到该队列时，未完成的任务的总数就会增加。
当使用者线程调用 task_done() 以表示检索了该项目、并完成了所有的工作时，那么未完成的任务的总数就会减少。
当未完成的任务的总数减少到零时，join() 就会结束阻塞状态。

---

###　示例

　* 示例

     <pre><code>
    def worker():
        while True:
            item = q.get()
            do_work(item)
            q.task_done()
    q = Queue()
    for i in range(num_worker_threads):
         t = Thread(target=worker)
         t.daemon = True
         t.start()
    for item in source():
        q.put(item)
    q.join()
     </code></pre>

