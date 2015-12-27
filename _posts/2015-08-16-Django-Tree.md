---
layout: post
title:  "Django Tree表"
date:   2015-08-16 19:51:39
categories: Django
excerpt: Django Tree表
---

* content
{:toc}


## 序

Django 大致有两种方法处理tree表,下面就简略介绍下这两种方法

---

# 迭代法

 * 定义模型
     <pre><code>
    class Food(models.Model):
        title= models.CharField(max_length=50)
        parent= models.ForeignKey("self", blank=True, null=True, related_name="children")
        def __unicode__(self):
            return self.title
    </code></pre>

 * 定义递归，生成待处理的列表
      <pre><code>
def display(foods):
    display_list= []
    for food in foods:
        display_list.append(food.title)
        children= food.children.all()
        if len(children) > 0:
            display_list.append(display(food.children.all()))
    return display_list
        </code></pre>

 * 注册model 在admin中增加一些数据

 * 处理函数
      <pre><code>
def shanyjhello(request):
    foods= Food.objects.filter(parent=None)
    var= display(foods)
    return render_to_response('syj.html', {'var': var})
        </code></pre>

 * 模板显示：
       <pre><code>
{{ var\|unordered_list }}
        </code></pre>

---

# MPTT法

 * 安装和配置：

   * pip install django-mptt

   * 加入mptt至install app中

