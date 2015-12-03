---
layout: post
title:  "Python APScheduler定时任务框架"
date:   2015-07-06 20:14:54
categories: Python
excerpt: Python APScheduler定时任务框架。
---

* content
{:toc}


## APScheduler

**APScheduler**一个Python定时任务框架,可以用来实现date、interval和cron操作。

---

### 安装

* pip install apscheduler


---

### 组件

* **触发器(trigger)**:包含调度逻辑，每一个作业有它自己的触发器，用于决定接下来哪一个作业会运行。

* **作业存储(job store)**:存储被调度的作业，默认的作业存储是简单地把作业保存在内存中，其他的作业存储是将作业保存在数据库中。

* **执行器(executor)**:处理作业的运行，他们通常通过在作业中提交制定的可调用对象到一个线程或者进城池来进行。当作业完成时，执行器将会通知调度器。

* **调度器(scheduler)**:是其他的组成部分。你通常在应用只有一个调度器，应用的开发者通常不会直接处理作业存储、调度器和触发器，相反，调度器提供了处理这些的合适的接口。配置作业存储和执行器可以在调度器中完成，例如添加、修改和移除作业。


---

### 原则

- **调度器选择**:这取决于你的应用环境和你使用APScheduler的目的。通常最常用的两个：

  * –BlockingScheduler: 当调度器是你应用中唯一要运行的东西时使用。

  * –BackgroundScheduler: 当你不运行任何其他框架时使用，并希望调度器在你应用的后台执行。

- **作业存储选择**:你需要决定是否需要作业持久化。

  * 如果你总是在应用开始时重建job，你可以直接使用默认的作业存储（MemoryJobStore).

  * 如果你需要将你的作业持久化，以避免应用崩溃和调度器重启时，你可以根据你的应用环境来选择具体的作业存储。例如：使用Mongo或者SQLAlchemyJobStore （用于支持大多数RDBMS）

- **执行器选择**:通常有两种:

  * 默认为ThreadPoolExecutor 通常用于大多数用途。

  * 如果你的工作负载中有较大的CPU密集型操作，你可以考虑用ProcessPoolExecutor来使用更多的CPU核。

  * 你也可以在同一时间使用两者，将进程池调度器作为第二执行器。



---

### 常用方法

* **添加作业**：

  * 使用add_job():
scheduler.add_job(myfunc, 'interval', minutes=2)

  * 使用装饰器：@sched.scheduled_job('cron', id='my_job_id', day='last sun')

  * add_job的第二个参数是trigger，它管理着作业的调度方式。它可以为date, interval或者cron。对于不同的trigger，对应的参数也相同。


* **移除作业**：

  * scheduler.remove_job('my_job_id')

* **暂停和继续作业**:

  * apscheduler.schedulers.base.BaseScheduler.pause_job()
apscheduler.schedulers.base.BaseScheduler.resume_job()

* **获得job列表**：

  * scheduler.get_jobs()

* **关闭调度器**：

  * scheduler.shutdown(wait=False)
默认情况下调度器会等待所有正在运行的作业完成后，关闭所有的调度器和作业存储。如果你不想等待，可以将wait选项设置为False。

---

### 三种调度

* **cron  定时调度**:

  * year: 4位数字

  * month: 月 (1-12)

  * day: 天 (1-31)

  * week: 标准周 (1-53)

  * day_of_week: 周中某天 (0-6 or mon,tue,wed,thu,fri,sat,sun)

  * hour: 小时 (0-23)

  * minute:分钟 (0-59)

  * second: 秒 (0-59)

  * start_date: 最早执行时间

  * end_date: 最晚执行时间

  * timezone: 执行时间区间

* **interval  间隔调度**:

  * weeks: 每隔几周执行一次

  * days: 每隔几天执行一次

  * hours: 每隔几小时执行一次

  * minutes: 每隔几分执行一次

  * seconds: 每隔几秒执行一次

  * start_date: 最早执行时间

  * end_date: 最晚执行时间

  * timezone: 执行时间区间

* **date  定时调度**:

  * 最基本的一种调度，作业只会执行一次。

  * run_date: 在某天执行任务

  * timezone: 在某段时间执行任务

---

### 时间格式

    Expression	Field	Description
    *	         any	Fire on every value
    */a	         any	Fire everyavalues, starting from the minimum
    a-b	         any	Fire on any value within thea-brange (a must be smaller than b)
    a-b/c	     any	Fire everycvalues within thea-brange
    xthy	     day	Fire on thex-th occurrence of weekdayywithin the month
    lastx	     day	Fire on the last occurrence of weekdayxwithin the month
    last	     day	Fire on the last day within the month
    x,y,z	     any	Fire on any matching expression

---

### 案例

* 可以先创建调度器，再配置和添加作业，这样你可以在不同的环境中得到更大的灵活性。

* 下面是一个简单使用BlockingScheduler，并使用默认内存存储和默认执行器。
(默认选项分别是MemoryJobStore和ThreadPoolExecutor，其中线程池的最大线程数为10)。配置完成后使用start()方法来启动。

<pre><code>
def my_job():
    print 'shanyj'
sched = BlockingScheduler()
sched.add_job(my_job, 'interval', seconds=5)
sched.start()
</code></pre>

* 复杂的配置：

<pre><code>
from pymongo import MongoClient
from apscheduler.schedulers.blocking import BlockingScheduler
from apscheduler.jobstores.mongodb import MongoDBJobStore
from apscheduler.jobstores.memory import MemoryJobStore
from apscheduler.executors.pool import ThreadPoolExecutor, ProcessPoolExecutor
def my_job():
    print 'shanyj'

host = '127.0.0.1'
port = 27017
client = MongoClient(host, port)

jobstores = {
'mongo': MongoDBJobStore(collection='job', database='test', client=client),
'default': MemoryJobStore()
}

executors = {
'default': ThreadPoolExecutor(10),
'processpool': ProcessPoolExecutor(3)
}

job_defaults = {
'coalesce': False,
'max_instances': 3
}

scheduler = BlockingScheduler(jobstores=jobstores, executors=executors, job_defaults=job_defaults)
scheduler.add_job(my_job, 'interval', seconds=5)
try:
    scheduler.start()
except SystemExit:
    scheduler.shutdown()
    client.close()
</code></pre>
