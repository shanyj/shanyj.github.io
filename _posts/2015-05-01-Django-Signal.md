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

之前研究了登录和登出逻辑，但是具体登陆处理函数没有给出，当不仅仅满足于Django自带的登录函数功能时，需要重写验证模式，自己研究并试用了下，暂时只更改改验证模式，而不更改user的属性，具体方式如下：

---

### model

* 我们使用新的model来进行用户验证，而代替Django自带的User model

* 在任意app的model中加入新的账户类如account1类，同时定义一个类中的函数

    <pre><code>
    def is_authenticated(self):
         return True
     </code></pre>