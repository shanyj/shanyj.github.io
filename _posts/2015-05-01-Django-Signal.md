---
layout: post
title:  "Django Signal 信号机制"
date:   2015-05-01 20:24:41
categories: Django
excerpt: Django Signal 信号机制
---

* content
{:toc}


## 序

django 包含一个信号机制,用来链接框架中发生的事件与得到通知的应用之间的逻辑。简单地说, 在特定事件发生时, 使用signal发送者能够通知一系列接收者(一个或者多个)来进行相应的操作。

---

### Signal类

 * 使用Signal前我们需要实例化一个Signal实例

    <pre><code>
    from django.dispatch import Signal
    signalshan=Signal(providing_args=['shanyj'])
     </code></pre>

---

### 发送信号

 * 当需要通知接受者时，首先需要发送者来发送Signal

 * 在某方法中尽心信号发送使用了send（）方法，同事发送需要传递的格外信息

    <pre><code>
    def shanyjsignal(request,hello='none'):
        a='shanyj'
        signalshan.send(sender=object,shanyj=a)
        # send方法第一个参数为sender，可以为对象.__class__ ,self（在类中的方法才可以）,或者直接写类名
        return HttpResponse(request.user)
     </code></pre>

---

### 接受信号

 * 接受信号时，需要与某一特定函数进行绑定，大致有两种方法

   * 使用装饰器：

    <pre><code>
    from django.dispatch import receiver

    @receiver(signalshan)
    def what(sender,shanyj,**kwargs):
        #执行函数第一个参数为sender
        print shanyj
     </code></pre>

   * 使用connect方法：

   <pre><code>
    signalshan.connect(what)
    #可以加第二个参数，只接受该类的sender
    </code></pre>

---

### 参数获取

 * 参数可以通过**kwargs来进行获取

<pre><code>
  upload_file_successful.send(sender=None,
                                    repo_id=repo_id,
                                    file_path=file_path,
                                    owner=owner)
    @receiver(upload_file_successful)
    def add_upload_file_msg_cb(sender, **kwargs):
        repo_id = kwargs.get('repo_id', None)
        file_path = kwargs.get('file_path', None)
        owner = kwargs.get('owner', None)
</code></pre>


