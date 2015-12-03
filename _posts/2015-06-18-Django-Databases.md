---
layout: post
title:  "Django 多数据库"
date:   2015-06-18 21:30:27
categories: Django
excerpt: Django 多数据库
---

* content
{:toc}


## Django多数据库

---

### 配置数据库

* 在settings中进行配置。

<pre><code>
DATABASES = {
    #之所以这里仍然保留default数据库，是因为如果要使用Django的Auth应用或者Admin应用
    #我希望它默认将数据存放在default.db中，而不与其他数据搞混
     'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': 'db/default.db',
        'USER': '',
        'PASSWORD': '',
        'HOST': '',
        'PORT': '',
    },

    #提供给userApp使用
     'userdb': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': 'db/user.db',
        'USER': '',
        'PASSWORD': '',
        'HOST': '',
        'PORT': '',
    },

     'essaydb': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': 'db/essay.db',
        'USER': '',
        'PASSWORD': '',
        'HOST': '',
        'PORT': '',
    },
}
</code></pre>

---

### 配置class

* 新建dbsettings.py , 编写下面 , class一般只改最后一个方法

<pre><code>
class appdb(object):
    def db_for_read(self, model, **hints):
        #该方法定义读取时从哪一个数据库读取
        return self.__app_router(model)

    def db_for_write(self, model, **hints):
        #该方法定义写入时从哪一个数据库读取，如果读写分离，可再额外配置
        return self.__app_router(model)

    def allow_relation(self, obj1, obj2, **hints):
        #该方法用于判断传入的obj1和obj2是否允许关联，可用于多对多以及外键
        #同一个应用同一个数据库
        if obj1._meta.app_label == obj2._meta.app_label:
            return True
        #User和Essay是允许关联的
        elif obj1._meta.app_label in ('userApp','essayApp') and obj2._meta.app_label in ('userApp','essayApp'):
            return True

    def allow_syncdb(self, db, model):
        #该方法定义数据库是否能和名为db的数据库同步
        return self.__app_router(model) == db

   #添加一个私有方法用来判断模型属于哪个应用，并返回应该使用的数据库
    def __app_router(self, model):
        if model._meta.app_label == 'userApp':
            return 'userdb'
        elif model._meta.app_label == 'essayApp':
            return 'essaydb'
        else :
            return 'default'
</code></pre>


---

### 将数据库class添加至settings

* DATABASE_ROUTERS = ['dbsettings.appdb']

---

### 同步数据库

* 使用下列方法分别同步不同数据库：

<pre><code>
python manage.py syncdb #默认同步的数据库为'default'
python manage.py syncdb --database=userdb  #为userdb同步数据
python manage.py syncdb --database=essaydb #为essaydb同步数据
</code></pre>

* 反向同步数据库

<pre><code>
python manage.py inspectdb --database=userdb > shanyjuser/models.py
</code></pre>

  * 注意database＝后面是settings里的名字

---

### model的class meta

* 每个model的class meta
中 app_label = 'myapp'
应和（2）中的最后一个方法相匹配！

---

### 使用底层sql

* 除了自带的model之外还可以使用底层sql

  * 针对单数据库
    <pre><code>
    from django.db import connection, transaction
        cursor = connection.cursor()
        # 数据修改操作——提交要求
        cursor.execute("UPDATE bar SET foo = 1 WHERE baz = %s", [self.baz])
        transaction.commit_unless_managed()
        # 或者 transaction.set_dirty()
        # 数据检索操作,不需要提交
        cursor.execute("SELECT foo FROM bar WHERE baz = %s", [self.baz])
        row = cursor.fetchone()
        return row
    </code></pre>

  * 针对多数据库
    <pre><code>
    from django.db import connections
    cursor = connections['my_db_alias'].cursor()
    cursor.execute("UPDATE bar SET foo = 1 WHERE baz = %s", [self.baz])
    transaction.commit_unless_managed(using='my_db_alias')
    </code></pre>
