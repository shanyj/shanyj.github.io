---
layout: post
title:  "Django Site多站点"
date:   2015-05-12 21:11:42
categories: Django
excerpt: Django Site多站点
---

* content
{:toc}


## 序

本文主要介绍Django多站点的使用和一些注意事项

---

### 添加Site

 * 首先需要进行安装，在settings中INSTALL_APP添加django.contrib.sites，并生成对应的表

 * 每个Site对象都具有name和domain，在表中添加对应的site实例即可

 * 在不同站点的settings中添加SITE_ID为指定的site的id号即可

---

### 定义model

 * 当具有site时，就可以定义含有site多对多类型的model

    <pre><code>
    class ok(models.Model):
        headline = models.CharField(max_length=200)
        sites = models.ManyToManyField(Site)
    </code></pre>

---

### 取数据

 * 当从含有site这个field的model中取数据时，就可以只去与本站相关联的数据了

    <pre><code>
    from django.conf import settings
    from books.models import ok

    def ok1(request):
        a=ok.objects.filter(site__id=settings.SITE_ID)
        return HttpResponse(a)
    </code></pre>

---

### CurrentSiteManager

 * CurrentSiteManager可以自动过滤当前site_id的信息

    <pre><code>
    class Photo(models.Model):
        photo = models.FileField(upload_to='/home/photos')
        site = models.ForeignKey(Site)
        objects = models.Manager()
        on_site = CurrentSiteManager()
    </code></pre>

 * 按照上面的代码，Photo.objects.filter(site__id=settings.SITE_ID)和Photo.on_site.all()等价

 * 当model中对应的外键名字不为site时，使用on_site = CurrentSiteManager('publish_on')来指定

---

### 自动判断

 * current_site = Site.objects.get_current()可以取出当前站点的site

    <pre><code>
    current_site = Site.objects.get_current()
    if current_site.domain == 'foo.com':
         # Do something
    else:
         # Do something else.
    </code></pre>