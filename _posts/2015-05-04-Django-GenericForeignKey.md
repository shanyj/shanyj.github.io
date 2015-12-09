---
layout: post
title:  "Django GenericForeignKey"
date:   2015-05-04 19:32:29
categories: Django
excerpt: Django GenericForeignKey
---

* content
{:toc}


## 序

最近在项目的一个工作流流程中大量的使用到了GenericForeignKey和 Content Type，主要用于在两个model之间建立一个关联的model，于是做了如下的小结。

---

### model

 * 首先我们需要定义两个需要关联的model

    <pre><code>
    from django.contrib.contenttypes import generic
    class music(models.Model):
        music_url = models.CharField(max_length=20)
        events = generic.GenericRelation('owner')
        #加这句的目的是在该实例删除时owner中的记录也会删除

    class vedio(models.Model):
        video_url = models.CharField(max_length=20)
        events = generic.GenericRelation('owner')
     </code></pre>

---

### 中间model

 * 建立与两个model建立联系的新的model

    <pre><code>
   class owner(models.Model):
        user = models.ForeignKey(User)
        content_type = models.ForeignKey(ContentType)
        object_id = models.PositiveIntegerField()
        event_object = generic.GenericForeignKey('content_type', 'object_id')
     </code></pre>

 * 通俗一点的理解，content_type定位了一个表，而object_id定位了一个id，event_object就相当于将'content_type', 'object_id'结合起来从而定位了一个model中的实例

---

### 存储

 * 之前讲过Django的signal，django.db.models.signals.pre_save & django.db.models.signals.post_save分别在一个Model的save()方法之前和之后触发，我们可以应用这个来实现自动存储

    <pre><code>
    from django.db.models.signals import post_save

    def save_owner(sender, instance, **kwargs):
        obj = instance
        publisher=User.objects.all()[0]
        event = owner(user=publisher,event_object=obj)
        event.save()

    post_save.connect(save_owner,sender=music)
    post_save.connect(save_owner,sender=vedio)
     </code></pre>
