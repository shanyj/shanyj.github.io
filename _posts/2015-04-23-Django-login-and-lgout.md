---
layout: post
title:  "Django login/lgout 登录和登出"
date:   2015-04-23 20:41:29
categories: Django
excerpt: Django login/lgout 登录和登出
---

* content
{:toc}


## 序

需要做用户登录，于是研究了下Django的登录和登出逻辑，发现还是挺简单实用的。具体的登录和登出实现如下：

---

### login/lgout方法

* Django已经为我们封装好了login和lgout函数，只需要我们在url.py中直接调用即可，具体使用方法如下：

    <pre><code>
    from django.contrib.auth.views import login, logout

    urlpatterns = patterns('',
        (r'^accounts/login/$', login),
        (r'^accounts/logout/$', logout),
    )
    </code></pre>

* 按照常规，登录url为accounts/login/，登出为accounts/logout/

---

### 登录模版

* login模版地址默认为template／registration／login.html

* 需要在login模版中编写登录表单，格式如下：

    <pre><code>
    < form action="" method="post">{ % csrf_token % }
    < label for="username">User name:</label>
    < input type="text" name="username" value="" id="username">
    < label for="password">Password:</label>
    < input type="password" name="password" value="" id="password">
    < input type="submit" value="login" />
    < /form>
    </code></pre>

---

### 重定向

* 登录验证成功后，默认会自动重定向到accounts／profile，需要编写该url对应的view函数

---

### CSRF

* 模版表单中通常加入｛％ csrf_token ％｝确保安全，同时在settings中间键中添加csrf中间键