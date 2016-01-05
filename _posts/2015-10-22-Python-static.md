---
layout: post
title:  "Django 静态文件"
date:   2015-10-22 19:43:42
categories: Django
excerpt: Django 静态文件
---

* content
{:toc}


## 序

静态文件指像css,js,images之类的文件,在Django里面静态文件的处理十分简单，这里就mark一下

---

### 安装和调用

 * installapp中加入 django.contrib.staticfiles

 * TEMPLATE_CONTEXT_PROCESSORS中加入'django.core.context_processors.media'

---

### media&static

 * 静态文件的处理又包括STATIC和MEDIA两类

 * MEDIA:

   * 指用户上传的文件，比如在Model里面的FileFIeld，ImageField上传的文件

   * 如果定义MEDIA_ROOT=c:\temp\media，那么File=models.FileField(upload_to="abc/")，上传的文件就会被保存到c:\temp\media\abc

   * MEDIA_ROOT必须是本地路径的绝对路径,MEDIA_ROOT=os.path.join(PROJECT_PATH,'media/').replace('\\','/')

   * MEDIA_URL是指从浏览器访问时的地址前缀，MEDIA_URL="/media/"

 * STATIC:

   * 主要指的是如css,js,images这样文件

   * STATIC_ROOT与MEDIA_ROOT位置不能一样

   * 在每个app所在文夹均可以建立一个static文件夹，
              然后当运行collectstatic时，Django会遍历INSTALL_APPS里面所有app的static文件夹，
              将里面所有的文件复制到STATIC_ROOT

 * 一般在和templates相同的位置上建立static和media目录

---

### 使用

 * settings配置
 <pre><code>
    MEDIA_URL="/media/"
    STATIC_URL="/static/"
    STATICFILES_DIRS = (
        os.path.join(os.path.dirname(__file__), "static"),
    )

 </code></pre>

 * view调用：render_to_response('hello.html',RequestContext(request))

 * 模板调用
 <pre><code>
     { % load staticfiles % }
    < img src="{ % static 'images/hi.jpg' % }" / >
 </code></pre>