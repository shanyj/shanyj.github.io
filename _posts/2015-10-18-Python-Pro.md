---
layout: post
title:  "Python 进程及同步"
date:   2015-10-18 21:02:32
categories: Python
excerpt: Python 进程及同步
---

* content
{:toc}


## 序

这里主要讲解下python中进程和进程同步的一些知识

---

### 进程模块

 * 创建进程的类：Process([group [, target [, name [, args [, kwargs]]]]])，target表示调用对象，args表示调用对象的位置参数元组。
kwargs表示调用对象的字典。name为别名

 * 方法：is_alive()、join([timeout])、run()、start()、terminate()

 * 属性：authkey、daemon（要通过start()设置）、exitcode(进程在运行时为None、如果为–N，表示被信号N结束）、name、pid

 * 例子
 <pre><code>
import multiprocessing
import time
def worker(interval):
    n = 5
    while n > 0:
        print("The time is {0}".format(time.ctime()))
        time.sleep(interval)
        n -= 1
if __name__ == "__main__":
    p = multiprocessing.Process(target = worker, args = (3,))
    p.start()
    print "p.pid:", p.pid
    print "p.name:", p.name
    print "p.is_alive:", p.is_alive()

print("The number of CPU is:" + str(multiprocessing.cpu_count()))
for p in multiprocessing.active_children():
    print("child   p.name:" + p.name + "\tp.id" + str(p.pid))
print "END!!!!!!!!!!!!!!!!!"
 </code></pre>

 * 将进程定义为类
 <pre><code>
import multiprocessing
import time
class ClockProcess(multiprocessing.Process):
    def __init__(self, interval):
        multiprocessing.Process.__init__(self)
        self.interval = interval

    def run(self):
        n = 5
        while n > 0:
            print("the time is {0}".format(time.ctime()))
            time.sleep(self.interval)
            n -= 1
if __name__ == '__main__':
    p = ClockProcess(3)
    p.start()
 </code></pre>

 * 当子进程设置了daemon=True属性，主进程结束，它们就随着结束了

---

### with语句

 * with 语句适用于对资源进行访问的场合，确保不管使用过程中是否发生异常都会执行必要的“清理”操作，
释放资源，比如文件使用后自动关闭、线程中锁的自动获取和释放等

 * with所求值的对象必须有一个__enter__()方法，一个__exit__()方法。
紧跟with后面的语句被求值后，返回对象的__enter__()方法被调用，这个方法的返回值将被赋值给as后面的变量。
当with后面的代码块全部被执行完之后，将调用前面返回对象的__exit__()方法

 * 例子
 <pre><code>
class Sample:
    def __enter__(self):
        print "In"
        return "Foo"
    def __exit__(self, type,value, trace):
        print "out"
def get_sample():
    return Sample()
with get_sample() as sample:
    print "sample:"
    print sample

结果
In
sample:
Foo
out
 </code></pre>

---

### lock

 * Lock对象的状态可以为locked和unlocked
使用acquire()设置为locked状态；
使用release()设置为unlocked状态

 * 如果当前的状态为unlocked，则acquire()会将状态改为locked然后立即返回。
当状态为locked的时候，acquire()将被阻塞直到另一个线程中调用release()来将状态改为unlocked，然后acquire()才可以再次将状态置为locked

 * 例子
 <pre><code>
import multiprocessing
import sys
def worker_with(lock, f):
    with lock:
        fs = open(f, 'a+')
        n = 10
        while n > 1:
            fs.write("Lockd acquired via with\n")
            n -= 1
        fs.close()

def worker_no_with(lock, f):
    lock.acquire()
    try:
        fs = open(f, 'a+')
        n = 10
        while n > 1:
            fs.write("Lock acquired directly\n")
            n -= 1
        fs.close()
    finally:
        lock.release()

if __name__ == "__main__":
    lock = multiprocessing.Lock()
    f = "file.txt"
    w = multiprocessing.Process(target = worker_with, args=(lock, f))
    nw = multiprocessing.Process(target = worker_no_with, args=(lock, f))
    w.start()
    nw.start()
    print "end"
 </code></pre>

 * Lock.acquire(blocking=True, timeout=-1),blocking参数表示是否阻塞当前线程等待，timeout表示阻塞时的等待时间 。
如果成功地获得lock，则acquire()函数返回True，否则返回False，timeout超时时如果还没有获得lock仍然返回False

---

### Semaphore

 * Semaphore管理一个内置的计数器，
每当调用acquire()时内置计数器-1；
调用release() 时内置计数器+1

 * 计数器不能小于0；当计数器为0时，acquire()将阻塞线程直到其他线程调用release()

 * 例子
 <pre><code>
def worker(s, i):
    s.acquire()
    print(multiprocessing.current_process().name + "acquire");
    time.sleep(i)
    print(multiprocessing.current_process().name + "release\n");
    s.release()

if __name__ == "__main__":
    s = multiprocessing.Semaphore(2)
    for i in range(5):
        p = multiprocessing.Process(target = worker, args=(s, i*2))
        p.start()
 </code></pre>

---

### Event

 * Event内部包含了一个标志位，初始的时候为false。
可以使用使用set()来将其设置为true；
或者使用clear()将其从新设置为false

 * 可以使用is_set()来检查标志位的状态

 * 另一个最重要的函数就是wait(timeout=None)，用来阻塞当前线程，直到event的内部标志位被设置为true或者timeout超时

 * 例子
 <pre><code>
def wait_for_event_timeout(e, t):
    print("wait_for_event_timeout:starting")
    e.wait(t)  //或e.wait()
    print("wait_for_event_timeout:e.is_set->" + str(e.is_set()))

if __name__ == "__main__":
    e = multiprocessing.Event()
    w2 = multiprocessing.Process(name = "non-block",
            target = wait_for_event_timeout,
            args = (e, 2))
    w2.start()
    time.sleep(3)
    e.set()
    print("main: event is set")
 </code></pre>