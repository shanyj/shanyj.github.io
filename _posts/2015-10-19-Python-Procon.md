---
layout: post
title:  "Python 进程间通信"
date:   2015-10-19 22:03:33
categories: Python
excerpt: Python 进程间通信
---

* content
{:toc}


## 序

这里主要讲解下python中进程间通信的一些手段

---

### Queue

 * put方法用以插入数据到队列中，put方法还有两个可选参数：blocked和timeout。
如果blocked为True（默认值），并且timeout为正值，该方法会阻塞timeout指定的时间，直到该队列有剩余的空间。
如果超时，会抛出Queue.Full异常。如果blocked为False，但该Queue已满，会立即抛出Queue.Full异常

 * get方法可以从队列读取并且删除一个元素。同样，get方法有两个可选参数：blocked和timeout。
如果blocked为True（默认值），并且timeout为正值，那么在等待时间内没有取到任何元素，会抛出Queue.Empty异常。
如果blocked为False，有两种情况存在，如果Queue有一个值可用，则立即返回该值，否则，如果队列为空，则立即抛出Queue.Empty异常

 * 例子
 <pre><code>
import multiprocessing
def writer_proc(q):
    try:
        q.put(1, block = False)
    except:
        pass
def reader_proc(q):
    try:
        print q.get(block = False)
    except:
        pass
if __name__ == "__main__":
    q = multiprocessing.Queue()
    writer = multiprocessing.Process(target=writer_proc, args=(q,))
    writer.start()
    reader = multiprocessing.Process(target=reader_proc, args=(q,))
    reader.start()
    reader.join()
    writer.join()
 </code></pre>

---

### Pipe

 * Pipe方法返回(conn1, conn2)代表一个管道的两个端

 * Pipe方法有duplex参数，如果duplex参数为True(默认值)，那么这个管道是全双工模式，也就是说conn1和conn2均可收发。
duplex为False，conn1只负责接受消息，conn2只负责发送消息

 * send和recv方法分别是发送和接受消息的方法。
例如，在全双工模式下，可以调用conn1.send发送消息，conn1.recv接收消息。
如果没有消息可接收，recv方法会一直阻塞。如果管道已经被关闭，那么recv方法会抛出EOFError

 * 例子
 <pre><code>
def proc1(pipe):
    while True:
        for i in xrange(10000):
            print "send: %s" %(i)
            pipe.send(i)
            time.sleep(1)

def proc2(pipe):
    while True:
        print "proc2 rev:", pipe.recv()
        time.sleep(1)

if __name__ == "__main__":
    pipe = multiprocessing.Pipe()
    p1 = multiprocessing.Process(target=proc1, args=(pipe[0],))
    p2 = multiprocessing.Process(target=proc2, args=(pipe[1],))
    p1.start()
    p2.start()
    p1.join()
    p2.join()
 </code></pre>

---

### Pool

 * Pool可以提供指定数量的进程，供用户调用，当有新的请求提交到pool中时，如果池还没有满，
那么就会创建一个新的进程用来执行该请求；但如果池中的进程数已经达到规定最大值，那么该请求就会等待，直到池中有进程结束，才会创建新的进程来它

 * apply_async(func[, args[, kwds[, callback]]]) 它是非阻塞，apply(func[, args[, kwds]])是阻塞的

 * close() : 关闭pool，使其不在接受新的任务。只接受apply_async(func, (msg, ))剩余的

 * terminate() : 结束工作进程，不在处理未完成的任务

 * join() : 主进程阻塞，等待子进程的退出， join方法要在close或terminate之后使用

 * 非阻塞例子
 <pre><code>
import multiprocessing
import time
def func(msg):
    print "msg:", msg
    time.sleep(3)
    print "end"

if __name__ == "__main__":
    pool = multiprocessing.Pool(processes = 3)
    for i in xrange(4):
        msg = "hello %d" %(i)
        pool.apply_async(func, (msg, ))   #维持执行的进程总数为processes，当一个进程执行完毕后会添加新的进程进去

    pool.close()
    pool.join()   #调用join之前，先调用close函数，否则会出错。执行完close后不会有新的进程加入到pool,join函数等待所有子进程结束
    print "Sub-process(es) done."
 </code></pre>

 * 阻塞例子
 <pre><code>
def func(msg):
    print "msg:", msg
    time.sleep(3)
    print "end"

if __name__ == "__main__":
    pool = multiprocessing.Pool(processes = 3)
    for i in xrange(4):
        msg = "hello %d" %(i)
        pool.apply(func, (msg, ))

    pool.close()
    pool.join()
    print "Sub-process(es) done."

结果:
msg: hello 0
end
msg: hello 1
end
msg: hello 2
end
msg: hello 3
end
Sub-process(es) done.
 </code></pre>