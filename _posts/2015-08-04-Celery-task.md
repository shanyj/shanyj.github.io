 ---
layout: post
title:  "Celery 异步&定时任务"
date:   2015-08-04 19:41:34
categories: Celery Django
excerpt: Celery 异步&定时任务
---

* content
{:toc}


## 序

最近使用celery对项目进行改造，使用异步任务来发送通知，使用定时任务来处理过期文件，下面就主要记录在Django中使用Celery的要点

---

### 安装与配置

 * 安装
    <pre><code>
    pip install django-celery
    </code></pre>

 * 配置

    * 进行broker等配置：
    <pre><code>
    import djcelery
    djcelery.setup_loader()
    BROKER_URL = 'django://'   #当使用redis作为后端时'redis://localhost:6379/0'
    CELERYBEAT_SCHEDULER = 'djcelery.schedulers.DatabaseScheduler'　　＃定时任务存储地址
    </code></pre>

    * 添加至app并生成数据表：
    <pre><code>
        INSTALLED_APPS = (
       ...
       'djcelery',
       'kombu.transport.django',
       ...
    )
    </code></pre>

---

### 创建任务

 * 创建异步项目：名字放在tasks.py文件夹下

    <pre><code>
    from celery import task
    @task()
    def add(x, y):
        return x + y
    </code></pre>

 * settings.py中的djcelery.setup_loader()运行时,
    Celery便会查看所有INSTALLED_APPS中app目录中的tasks.py文件, 找到标记为task的function,
    并将它们注册为celery task

 * 调用命令：
     <pre><code>
     import myapp.tasks.add
     add.delay(2, 2)
    </code></pre>

---

### 执行任务

 *  python manage.py celery worker --loglevel=info

 *  执行定时任务时需要加上心跳python manage.py celery beat

 *  python manage.py celery worker -B（启动woker时自动启动beat）　

---

### 结果返回

<pre><code>
 result = add.delay(2, 2)
    if result.ready():
        print "Task has run"
        if result.successful():
            print "Result was: %s" % result.result
        else:
            if isinstance(result.result, Exception):
                print "Task failed due to raising an exception"
                raise result.result
            else:
                print "Task failed without raising exception"
</code></pre>

---

### 注意

 * 有时会遇到解决root用户无法启动celery的问题，需要在代码中加入platforms.C_FORCE_ROOT = True