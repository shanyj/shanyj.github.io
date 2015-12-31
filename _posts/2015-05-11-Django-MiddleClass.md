---
layout: post
title:  "Django 中间件"
date:   2015-05-11 20:18:33
categories: Django
excerpt: Django 中间件
---

* content
{:toc}


## 序

中间件，顾名思义，就是在处理一个Request前后需要执行的一系列函数，以下就是对Django中间件的一些总结

---

### 顺序

 * Django中间件执行顺序为，在request和view阶段从上到下处理，response和异常处理阶段从下到上处理

 * settings中MIDDLEWARE_CLASSES包含了当前使用的中间件

---

### 方法

 * __init__(self)：

 只在django运行时加载一次，如果抛出异常则将该中间件剔除，不对每个request单独处理，只需要self参数

 * process_request(self,request)：

 在接受request后并且view函数运行前执行的，应返回None或者Httpresponse对象，如果返回None继续执行view函数，返回Httpresponse则不执行view函数

 * process_view(self,request,view,args,kwargs)：

 其中args和kwargs是将传入view的参数

 * process_response(self,request,response)：

 view函数执行response之后，必须返回httpresponse对象

 * process_exception(self,request,exception)：

 view函数执行时抛出异常才会进行处理

---

### 例子

 <pre><code>
 class MultiHostMiddleware:
    def process_request(self, request):
        try:
            host = request.user
            if host == 'shanyj':
                return HttpResponse('aaaaa')
            response=HttpResponse('haha')
            response.set_cookie('favorite_color','lala')
            return response
        except KeyError:
            pass
    def process_response(self, request, response):
        try:
            if request.COOKIES['favorite_color']:
                return response
        except:
            return response
        else:
            return response
 </code></pre>