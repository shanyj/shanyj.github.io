---
layout: post
title:  "django-redis 缓存"
date:   2015-05-30 22:04:15
categories: Django第三方模块
excerpt: django-redis 缓存
---

* content
{:toc}


## 序

需要频繁对一个字段读取的时一般会将这个字段放入到缓存服务器上，而redis就是一个很好的选择，django-redis为我们使用redis作为缓存奠定了基础

---

### 安装

 * 安装Redis服务器端: sudo apt-get install redis-server

 * 安装redis for Django的插件: pip install django-redis

---

### settings配置

 * 在Django的settings中配置

    <pre><code>
     CACHES = {
        'default': {
            'BACKEND': 'redis_cache.cache.RedisCache',
            'LOCATION': 'redis://127.0.0.1:6379/1',
            "OPTIONS": {
                "CLIENT_CLASS": "redis_cache.client.DefaultClient",
                },
            },
        }
    </code></pre>

 * 当时用MemcachedCache时，配置类似

    <pre><code>
     CACHES = {
        'default': {
            'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
            'LOCATION': '127.0.0.1:11211',
            }
        }
    </code></pre>

---

### 使用

 * 使用方法参见 http://shanyj.github.io/2015/05/27/Django-Cache/

---

### 其他配置

 * 使用redis作为session后台：

     * SESSION_ENGINE = "django.contrib.sessions.backends.cache"      SESSION_CACHE_ALIAS = "default"