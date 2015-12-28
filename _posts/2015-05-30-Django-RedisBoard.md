---
layout: post
title:  "Django Redisboard"
date:   2015-05-30 22:03:14
categories: Django Redis
excerpt: Django Redisboard
---

* content
{:toc}


## 序

Django  的redisboard可以让我们很方便的控制Redis数据库，他在Django中的使用方法大致如下：

---

### 安装与运行

 * 安装
     <pre><code>
pip install django-redisboard
    </code></pre>
    在settings中加入
     <pre><code>
    INSTALLED_APPS += ("redisboard", )
    </code></pre>

 * 建表并收集静态文件

     <pre><code>
manage.py syncdb
manage.py collectstatic
    </code></pre>

---

### 配置

 * 过滤：REDISBOARD_DETAIL_FILTERS可以过滤需要获取的资源

 * EDISBOARD_DETAIL_FILTERS = ['.*']则表示获取所有资源

---

### 连接数据库

 * 在admin页面添加redis相关的host和port即可查看相应的redis


