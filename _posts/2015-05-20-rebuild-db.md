---
layout: post
title:  "Django 整合数据库"
date:   2015-05-20 21:02:22
categories: Django
excerpt: Django 整合数据库
---

* content
{:toc}


## 序

当尝试用Django来重构某一项目时，数据资源的整合十分关键，下面就介绍一下Django是如何进行数据库整合的

---

### inspectdb

 * inspectdb命令可以依据数据库结构来生成新的model

 *   python mysite/manage.py inspectdb > mysite/myapp/models.py 重定向输出到models.py

如：python manage.py inspectdb > models.py

---

### 注意事项

 * 命令不会自动转换manytomany类型

 * 当没有主键时自动增加id = models.IntegerField(primary_key=True)

 * 无法检测类型时会用textfield代替

 * 如果列名为django保留字，会做下面处理，以for为例：for_field = models.IntegerField(db_column='for'), 添加_field 并且db_column设置为原来名字

 * 需要手动调节一下model的顺序，比如某一个model引用了另一个model外键，另一个要在前面