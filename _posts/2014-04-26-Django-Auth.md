---
layout: post
title:  "Django Auth登陆认证"
date:   2015-04-26 22:03:21
categories: Django
excerpt: Django Auth登陆认证
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


---

### 认证函数

* 认证过程中使用我们自己编写的model，但是返回时依旧返回User

* 编写AuthBackend类，需要实现get_user和authenticate方法。

    <pre><code>
    class AuthBackend(object):
    def get_user(self,id):
        try:
            user=User.objects.get(pk=id)
        except:
            user=None
        return user

    def authenticate(self,username,password):
        try:
            user=Account1.objects.get(username=username)
            if password==user.shanyunji:
                return User.objects.get(username=username)
            else:
                return None
        except:
            return None
    </code></pre>

---

### 配置

* 编写好的AuthBackend类需要加入到settings中，在AUTHENTICATION_BACKENDS中加入自己编写好的类。

* AUTHENTICATION_BACKENDS中会依照顺序进行验证，如果第一个验证通过则通过，否则尝试下一个，只有所有认证都失败才失败。

