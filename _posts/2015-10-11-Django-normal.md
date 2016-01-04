---
layout: post
title:  "Django 通用视图"
date:   2015-10-11 21:02:18
categories: Django
excerpt: Django 通用视图
---

* content
{:toc}


## 序

这次mark一下Django通用视图的一些基本用法和简单地配置

---

### 重定向

 * 直接重定向

    <pre><code>
    from django.views.generic import TemplateView
    url('^about/$',TemplateView.as_view(template_name="hello.html"))
    </code></pre>

 * 重定向并赋值

    <pre><code>
    url('^about1/$',HomePageView.as_view())

    class HomePageView(TemplateView):
        template_name = "hello.html"

        #设置格外参数
        def get_context_data(self, **kwargs):
            context = super(HomePageView, self).get_context_data(**kwargs)
            context['next']='aaaaaaaaaaa'
            return context
    </code></pre>

---

### 显示model

 * 显示model

    <pre><code>
    url('^showpub/$',PubDetailView.as_view())

    from django.views.generic.list import ListView
    # 默认渲染 应用名/类名_list.html即books/publisher_list（小写）

    class PubDetailView(ListView):
        model=Publisher
        # model指定模型 queryset处理 因此可以在url中传任意参数供queryset处理
        queryset=Publisher.objects.filter(name='name')

        template_name = 'hello.html'
        #模版中对应model的是object_list

        def get_context_data(self, **kwargs):
            context=super(PubDetailView,self).get_context_data(**kwargs)
            context['next']=Publisher.objects.all
            # Publisher.objects.all 后面没有括号;这表示这是一个函数的调用,并没有真正调用它(通用视图将渲染时调用它)
            return context
    </code></pre>

 * 模板显示

    <pre><code>
    { % for object in object_list % }
    < h1 >标题：{ { object.name } }< /h1 >
    < p >内容：{ { object.address } }< /p >
    < p >发表人: { { object.city } }< /p >
    { % endfor % }
    </code></pre>
