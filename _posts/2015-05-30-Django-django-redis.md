---
layout: post
title:  "django-redis 缓存"
date:   2015-05-30 22:04:15
categories: Redis
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

 * timeout超时：

    <pre><code>
    "OPTIONS": {
        "SOCKET_CONNECT_TIMEOUT": 5,  # in seconds
        "SOCKET_TIMEOUT": 5,  # in seconds
    }
    </code></pre>

    前者是链接超时。后者是读写超时

 * 压缩支持：

    <pre><code>
    "OPTIONS": {
        "COMPRESS_MIN_LEN": 10,
        "COMPRESS_COMPRESSOR": lzma.compress,
        "COMPRESS_DECOMPRESSOR": lzma.decompress,
        "COMPRESS_DECOMPRESSOR_ERROR": lzma.LZMAError
    }
    </code></pre>

 * 锁：

    <pre><code>
    with cache.lock("somekey"):
        do_some_thing()
    </code></pre>

 * 生成器：

    <pre><code>
    >>> from django.core.cache import cache
    >>> cache.iter_keys("foo_*")
    <generator object algo at 0x7ffa9c2713a8>
    >>> next(cache.iter_keys("foo_*"))
    </code></pre>

 * 模糊匹配与删除：

    <pre><code>
    >>> from django.core.cache import cache
    >>> cache.keys("foo_*")
    ["foo_1", "foo_2"]
    >>> from django.core.cache import cache
    >>> cache.delete_pattern("foo_*")
    </code></pre>

 * 分布式：

    <pre><code>
    CACHES = {
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": [
            "redis://127.0.0.1:6379/1",
            "redis://127.0.0.1:6379/2",
        ],
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.ShardClient",
        }
    }
    }
    </code></pre>

 * 主从支持：

    <pre><code>
    "LOCATION": [
        "redis://127.0.0.1:6379/1",
        "redis://127.0.0.1:6378/1",
    ]
    </code></pre>
    这样配置的话第一个是主数据库,第二个是从数据库,要先在redis中配置主从关系


