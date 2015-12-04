---
layout: post
title:  "Django 扩展manage.py命令"
date:   2015-04-20 19:56:09
categories: Django
excerpt: Django 扩展manage.py命令
---

* content
{:toc}


## 序


由于项目中添加了诸如文档、系统管理员这样的角色，因此需要执行命令来手动创建这些高权限账号，这就需要我们手动扩展manage.py命令，大致流程如下：

---

### management

* 所有需要扩展的manage命令都放在management的commands文件夹下，因此需要在应用中建立management文件夹，之后在该文件夹下建立commands文件夹

* management和commands下都需要建立空的__init__.py文件

* 应用必须存在于INSTALLED_APPS中

* commands下以  命令名.py  建立python文件，一个命令对应一个文件

---

### Basecommand类

* 命令名.py 文件中编写格式如下：

    <pre><code>
    from django.core.management.base import BaseCommand
    class Command(BaseCommand):
        def handle(self, *args, **options):
            print args
            print options
    </code></pre>

  * 注意类名为Command、继承BaseCommand、方法名为handle

---

### 运行

* 进入 manage.py 目录下，运行 python manage.py hello a b c name＝shanyj
