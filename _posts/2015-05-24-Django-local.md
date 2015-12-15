---
layout: post
title:  "Django 国际化"
date:   2015-05-24 20:12:35
categories: Django
excerpt: Django 国际化
---

* content
{:toc}


## 序

Django有一套规范的国际化和本地化方法，可以方便世界各的用户使用产品，国际化和本地化使用方法如下

---

### py文件

 * ugettext()

   * 使用ugettext() 函数指定要翻译的字符串，惯例用_代替

   * 示例：
   <pre><code>
    from django.utils.translation import ugettext as _
    def translate(request):
        a = _('Today is %(month)s %(day)s.') % {'month': m, 'day': d}
        return HttpResponse(a)
   </code></pre>

 * gettext_noop()

   * 标记字符串为不翻译

   * django.utils.translation.gettext_noop(),这种情况下会以原始语言存在数据库等地，只在最后展示给用户时翻译

 * gettext_lazy()

   * 惰性翻译

   * django.utils.translation.gettext_lazy(),只有当值被访问时才翻译，而不是在gettext_lazy()调用时翻译，同样在数据库等地都不翻译

   * django模型中都使用惰性翻译

   * a=u"Hello %s" % ugettext_lazy("people"),可以进行unicode插入

 * ungettext()

   * 指定复数形势

   * 使用django.utils.translation.ungettext()，有三个参数：1.单数形式字符串翻译，2.复数形式的字符串翻译，3.对象的个数

---

### 模板代码

 * trans翻译

   * { % load i18n % }放在最前面，然后使用{ % trans "pingshen" % }

 * blocktrans翻译

   * { % blocktrans % }This string will have { { value } } inside.{ % endblocktrans % }

---

### 消息文件

 * django‐admin.py makemessages ‐l de ‐e html,txt ‐e xml    de为语言代码、-e为需要翻译的文件类型

 * 脚本在django根目录 or 项目根目录 or 应用根目录下执行。需要手动创建locale文件夹。

 * 编辑.po文件，msgid ""      msgstr "" 分别表示翻译前和翻译后的文字

 *  django‐admin.py makemessages ‐a更新语言信息，django‐admin.py compilemessages 编译语言信息

---

### 语言偏好

 * 当使用locatemiddleware django.middleware.locale.LocaleMiddleware中间键时， 中间件查找顺序 session的django_language、cookie、http请求头、settings中的language code

 * LANGUAGES = (('en', u'English'),('zh-cn',u'中文'))设置可选语种

---

### 重定向视图

 * url中(r'^i18n/', include('django.conf.urls.i18n'))，使得 /i18n/setlang/有效

 * 选择语言时，如果session启动，视图会将语言选择保存在session中，否则以django-language为key保存在cookie中

 * 选择完语言后，重定向到post的\< input name="next" \>值中，或者html头的refer中，再或者/

    <pre><code>
    < form action="/i18n/setlang/" method="post" >
        < input name="next" type="hidden" value="/next/page/"  >
        < select name="language" >
        { % for lang in LANGUAGES % }
        < option value="{{ lang.0 }}" > { { lang.1 } } </ option >
        { % endfor % }
        < /select >
        < input type="submit" value="Go"  >
    < /form >
    </code></pre>
