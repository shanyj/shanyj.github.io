---
layout: post
title:  "Django Tree表"
date:   2015-08-15 19:51:39
categories: Django
excerpt: Django Tree表
---

* content
{:toc}


## 序

Django 大致有两种方法处理tree表,下面就简略介绍下这两种方法

---

### 迭代法

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
{ { var \| unordered_list } }

---

### MPTT法

 * 安装和配置：

   * pip install django-mptt

   * 加入mptt至install app中

 * 定义模型

   * 普通模式
     <pre><code>
    class MPTTFood(MPTTModel):
        title= models.CharField(max_length=50)
        parent= models.ForeignKey("self", blank=True, null=True, related_name="children")
        def__unicode__(self):
            returnself.title
    </code></pre>

   * 实际上MPTTModel隐藏了四个变量：level，lft，rght和tree_id。大多数时候我们是用不到这几个变量的

   * 如果你的Model中parent变量名字不是"parent"时，应当在Model类中MPTT元类中指明
      <pre><code>
    class MPTTFood(MPTTModel):
        title= models.CharField(max_length=50)
        parent_food= models.ForeignKey("self", blank=True, null=True, related_name="children"
        classMPTTMeta:
            parent_attr= 'parent_food'
        def__unicode__(self):
            returnself.title
    </code></pre>

 * 管理
    <pre><code>
    from django.contrib import admin
    from mptt.admin import MPTTModelAdmin
    admin.site.register(MPTTFood, MPTTModelAdmin)
    </code></pre>

 * view方法
    <pre><code>
    def mptt(request):
        nodes= MPTTFood.objects.all()
        returnrender_to_response('mpttexample/mptt.html', {'nodes': nodes})
    </code></pre>

 * 模板
     <pre><code>
    { % load mptt_tags % }
    { % recursetree nodes % }
     < li >
            { { node.title } }
            < ul >
                { { children } }
            < /ul >
        { % endif % }
    < /li >
    { % endrecursetree % }
    </code></pre>
