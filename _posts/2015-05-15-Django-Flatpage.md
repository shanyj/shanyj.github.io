---
layout: post
title:  "Django 简单页面&重定向&个性化设置"
date:   2015-05-15 19:58:09
categories: Django
excerpt: Django 简单页面&重定向&个性化设置
---

* content
{:toc}


## 序

Django简单页面可以用来进行声明、条款等几乎不许改变内容的页面渲染，重定向负责页面重定向，人性化设置则是为了显示更美观

---

### 简单页面

 * 激活简单页面

   * 添加‘django.contrib.sites’，'django.contrib.flatpages'到INSTALLED_APPS

   * 添加'django.contrib.flatpages.middleware.FlatpageFallbackMiddleware'到MIDDLEWARE_CLASSES，通常放在列表最后，因为是最后的办法

   * 运行python manage.py syncdb生成简单页面表

 * 简单页面model

    <pre><code>
    class FlatPage(models.Model):
        url = models.CharField(max_length=100, db_index=True)
        title = models.CharField(max_length=200)
        content = models.TextField(blank=True)
        enable_comments = models.BooleanField()
        template_name = models.CharField(max_length=70, blank=True)
        registration_required = models.BooleanField()
        sites = models.ManyToManyField(Site)
    </code></pre>

    * url：不包含域名，如/about/contact/

    * title：标题

    * content：网页内容

    * enable_comments：是否允许评论

    * template_name：模版名称，如果未知定，则使用flatpages/default.html

    * registration_required：是否需要登陆

 * 最后渲染指定template即可，以title为例{{ flatpage.content|safe }}

---

### 重定向

 * 激活重定向

   * 将'django.contrib.redirects'加到INSTALLED_APPS

   * 将'django.contrib.redirects.middleware.RedirectFallbackMiddleware'加到MIDDLEWARE_CLASSES

   * 运行python manage.py syncdb生成重定向表

 * 设置model

   * 重定向model比较简单，只有site、oldpath、newpath三个属性

---

### 个性化数据

 * 激活个性化数据

   * 将django.contrib.humanize加入app中


 * 使用个性化数据

   * 使用前需要进行调用{ % load humanize % }

   * 使用apnumber：{ {1\|apnumber} }（1 变成 one ）

   * 使用intcomma：{ {1000000\|intcomma} }（4500 变成 4,500）

   * 使用intword：{ {1000000000000\|intword} }（1000000 变成 1.0 million ）

   * 使用ordinal：{ {1\|ordinal} }（1 变成 1st ）



