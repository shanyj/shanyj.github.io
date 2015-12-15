---
layout: post
title:  "Django 缓存"
date:   2015-05-27 21:58:09
categories: Django
excerpt: Django 缓存
---

* content
{:toc}


## 序

当需要从数据库频繁大量读取某一数据时，会导致页面反应时间大幅下降，此时可以采用缓存来进行优化，总结的缓存相关知识如下

---

### 分类

 * 内存缓存

   * 需要借助memcached或redis等

 * 数据库缓存

   * 需要先安装缓存表 即python manage.py createcachetable cache_table

 * 文件系统缓存

 * 本地缓存

   * 只利用内存缓存的优势

 * 仿缓存

   * 仅供开发使用

---

### 站点级缓存

 * 需要借助中间键，django.middleware.cache.UpdateCacheMiddleware，django.middleware.cache.FetchFromCacheMiddleware分别添加到中间键的第一和靠近最后位置，

 * 在setting中添加CACHE_MIDDLEWARE_SECONDS（每个页面缓存秒数），CACHE_MIDDLEWARE_KEY_PREFIX（可为空）

 * 缓存会自动缓存每个没有post和get请求的页面

---

### 视图级缓存

 * cache_page装饰器

 <pre><code>
 from django.views.decorators.cache import cache_page
 @cache_page(60 * 15)      //存900秒
 </code></pre>

 * 在url配置页面，(r'^foo/(\d{1,2})/$', cache_page(my_view, 60 * 15))

 * 装饰头

   * @vary_on_headers('User‐Agent', 'Cookie')：  根据User‐Agent和Cookie不同来进行分别缓存

   * @vary_on_cookie:  @vary_on_cookie＝  @vary_on_headers('Cookie')

   * @cache_control(private=True): 私人缓存，根据用户不同进行不同缓存

   * 注意顺序

   <pre><code>
   @cache_page(60*2)
   @vary_on_headers('User-Agent')
   </code></pre>

---

### 模块级缓存

 * 模块级缓存

 <pre><code>
    { % load cache % }
    { % cache 500 sidebar % }
    { % endcache % }
 </code></pre>

---

### 底层api

 * 底层API

    cache.set('haha',1,600)

    cache.incr('haha') //自增

    cache.decr('haha',10) //自减10

    cache.get('haha','过期了')  //当获取不到时返回 过期了

    cache.get_many(['haha','haha1','haha2'])  //返回多个

    cache.delete('a')