---
layout: post
title:  "Django User扩展"
date:   2015-04-28 20:57:51
categories: Django
excerpt: Django User扩展
---

* content
{:toc}


## 序

之前更改登录验证模式时返回的时Django自带的User model，当需要扩展业务而User model已经无法满足要求时，就需要扩展User类，收集后发现大致有三种方法进行User扩展。

---

### model

* 我们使用新的model来进行用户验证，而代替Django自带的User model

* 在任意app的model中加入新的账户类如account1类，同时定义一个类中的函数

    <pre><code>
    def is_authenticated(self):
         return True
     </code></pre>


