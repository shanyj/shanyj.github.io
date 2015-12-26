 ---
layout: post
title:  "Celery 分布式"
date:   2015-08-07 20:28:14
categories: Celery Python
excerpt: Celery 分布式
---

* content
{:toc}


## 序

Celery可以使用分布式，让不同的机器处理优先级不同的任务，在这里就做以下Celery分布式的小结

---

### 配置与实例化

 * Celery配置类
    <pre><code>
    from celery import Celery
    from kombu import Queue,Exchange
    class Config:
        CELERY_TASK_RESULT_EXPIRES=3600
        CELERY_TASK_SERIALIZER='json'
        CELERY_ACCEPT_CONTENT=['json']
        CELERY_RESULT_SERIALIZER='json'

        CELERY_DEFAULT_EXCHANGE = 'agent'
        CELERY_DEFAULT_EXCHANGE_TYPE = 'direct'
        CELERY_DEFAULT_QUEUE = 'm0'

        CELERT_QUEUES =  (
          Queue('m0',exchange='agent',routing_key='m0'),
          Queue('m1',exchange='agent',routing_key='m1'),
          Queue('m2',exchange='agent',routing_key='m2'),
        )
    </code></pre>

 * 实例化
 <pre><code>
    app = Celery('syj',broker='redis://localhost:6379/0',backend='redis://localhost:6379/0',)
    # 第二台机子需要修改broker和backend
    app.config_from_object(Config)
 </code></pre>

---

### 定义任务

 * 定义任务：
    <pre><code>
     @app.task
    def sendmail(mail):
        print 'aaaaaaaaaaaaaa'
        print('sending mail to %s...' % mail)
    </code></pre>

---

### 启动worker

 * 分别启动两个worker

   * celery -A syj worker -l info -Q m1

   * celery -A syj worker -l info -Q m2

---

### 发布任务

 * 发布任务到不同的redis queue

 *  sendmail.apply_async(args=['syj'],queue='m1',routing_key='m1') #发送至m1

 *  sendmail.apply_async(args=['syj'],queue='m2',routing_key='m2') #发送至m2

---

### 另一种方法

 * 也可以通过定义celery routers来直接指派任务的queue

    <pre><code>
        CELERY_ROUTES = {'syj.cansyj':{'queue':'m1'},
                'syj.canall':{'queue':'m2'},}
        @app.task()
        def cansyj():
            print 'aaaaaaaaaaaaaa'
        @app.task()
        def canall():
            print 'bbbbbbbbbbbbbb'
    </code></pre>