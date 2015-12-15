---
layout: post
title:  "Django 权限限制"
date:   2015-06-05 20:41:33
categories: Django
excerpt: Django 权限限制
---

* content
{:toc}


## 序

Django有着多种多样的权限限制方法来保证特定的用户才能执行特点的方法，下面就是一些小结

---

### 登陆限制

 * @login_required：

    * @login_required 的方法，当未登陆会跳转到登陆页面，已登陆则继续执行


---

### 权限限制

 * user_passes_test：

    * from django.contrib.auth.decorators import user_passes_test

    * 装饰器有两个参数,@user_passes_test(user_can_vote, login_url="/login/"),第一个是验证方法，第二个login_url是验证失败的跳转地址

    * 验证方法返回true 或 false，参数为User

    <pre><code>
    from django.contrib.auth.decorators import user_passes_test

    def user_can_vote(user):
        a=False
        if user.zm=='haha':
            a=True
        return a

    @user_passes_test(user_can_vote, login_url="/login/")
    def userallow(request):
        return HttpResponse('ha!')
    </code></pre>

 * permission_required:

    * @permission_required('polls.can_vote', login_url="/login/")

    * 其中第一个参数为，user.has_perm("polls.can_vote")里的权限名
