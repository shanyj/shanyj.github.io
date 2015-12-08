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

### 查看User类

* 使用get_user_model()可以查看使用的user类

* 在Django shell中执行下列代码，即可查看当前使用的User类

    <pre><code>
    from django.contrib.auth import get_user_model
    get_user_model()
     </code></pre>

---

### AbstractUser

* 使用AbstractUser可以在使用Django自带的User类的所有属性前提下进行扩展，具体使用方法如下

* 继承AbstractUser并增加新属性

    <pre><code>
    from django.contrib.auth.models import AbstractUser
    class NewUser(AbstractUser):
        shanyj = models.CharField(max_length=100)
    </code></pre>

 * 在settings中进行设置，AUTH_USER_MODEL = "myapp.NewUser"

 * 为了在admin界面中，方便使用，可在对应app的admin.py中加入

     <pre><code>
    from .models import NewUser
    admin.site.register(NewUser)
    </code></pre>

---

### AbstractBaseUser

 * AbstractBaseUser对User类进行了精简，只含有3个field: password, last_login和is_active. 如果你对django user model默认的first_name, last_name不满意, 或者只想保留默认的密码储存方式, 则可以选择这一方式.

 * AbstractBaseUser需要编写User model和UserManager方法

 * User model需要注意一下几点:

      * 设置UserManager objects = UserManager()，UserManager()编写方法稍后介绍

      * 设置USERNAME_FIELD，并且Field要有unique＝True

      * 设置get_full_name()方法

      * 设置get_short_name()方法

   <pre><code>
        class NextUser(AbstractBaseUser):
            shanyj = models.CharField(max_length=100,unique=True)
            a = models.CharField(max_length=25)
            is_active = models.BooleanField(default=True)
            is_staff = models.BooleanField(default=True)
            is_admin = models.BooleanField(default=False)

            objects = UserManager()

            USERNAME_FIELD = 'shanyj'

            def get_full_name(self):
                return self.shanyj
            def get_short_name(self):
                return self.shanyj
    </code></pre>

 * UserManager中需要重写两个方法，注意以下几点：

      * save()用到了using=self.db

      * 需要有user.set_password(password)

      *  user = self.model(shanyj=shanyj，…………)

 * 需要编写create_user和create_superuser方法


   <pre><code>
    class UserManager(BaseUserManager):
        def create_user(self, shanyj, password=None):
            if not shanyj:
              raise ValueError('Users must have an email address')
            user = self.model(
              shanyj=shanyj,
            )
            user.set_password(password)
            user.save(using=self._db)
            return user

        def create_superuser(self, shanyj, password=None):
            user = self.create_user(shanyj,password)
            user.is_admin = True
            user.save(using=self._db)
            return user

    </code></pre>

 * 在settings中进行设置，AUTH_USER_MODEL = "myapp.NewUser"

---

### User Profile

 * 使用外键的方式来对用户的属性进行扩展，增加新的model如下：

    <pre><code>
    class EasterProfile(models.Model):
        user = models.OneToOneField(settings.AUTH_USER_MODEL)
        favorite_ice_cream = models.ForeignKey(Flavor, null=True, blank=True
    </code></pre>

 * 我们可以使用user.easterprofile.favorite_ice_cream获取相应的profile (注意为小写)




