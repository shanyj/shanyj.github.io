---
layout: post
title:  "Django captcha 验证码"
date:   2015-10-27 20:23:34
categories: Django
excerpt: Django captcha 验证码
---

* content
{:toc}


## 序

登录功能中验证码是十分重要的，在这里就简单的介绍Django中验证码的实现

---

### 安装

 * pip install  django-simple-captcha

 * 配置

    <pre><code>
    INSTALLED_APPS中加入captcha

    urlpatterns += patterns('',
    url(r'^captcha/', include('captcha.urls')),
    )
    </code></pre>

---

### 建立表单

 * 建立表单

    <pre><code>
from django.core.cache import cache
from django import forms
from captcha.fields import CaptchaField

class TestForm(forms.Form):
    name=forms.CharField()
    def clean_name(self):
        clean_name=self.cleaned_data['name']
        if clean_name=='shanyj':
            return clean_name
        else:
            raise forms.ValidationError("aaaa!")

class CForm(forms.Form):
    captcha = CaptchaField()
    </code></pre>

---

### 验证表单

 * 验证表单

    <pre><code>
def some_view(request):
    form2=''
    if request.POST:
        if request.POST.get('captcha_0','na')!='na':
            form1=TestForm(request.POST)
            form2=CForm(request.POST)
            if form1.is_valid() and form2.is_valid():
                cache.delete('pass')
                return HttpResponse('ok')
            else:
                try:
                    cache.incr('pass')
                except:
                    cache.set('pass',1,3600)
        else:
            form1=TestForm(request.POST)
            if form1.is_valid():
                cache.delete('pass')
                return HttpResponse('ok')
            else:
                try:
                    cache.incr('pass')
                except:
                    cache.set('pass',1,3600)
    else:
        form1=TestForm()
    if cache.get('pass')>3:
        form2=CForm()
    return render_to_response('template.html',RequestContext(request,{'form1':form1,'form':form2}))
    </code></pre>

---

### 模板显示

 * 模板显示

    <pre><code>
    { % load staticfiles % }

    < form action='.' method='POST' >{ % csrf_token % }
        { { form1 } }
        { % if form.captcha % }
        { { form.captcha } }
        { % endif % }
        < input type="submit" />
        < button class='captcha-refresh'>刷新< /button>
        { % if form.captcha.errors % }
        <p>{ { form.captcha.errors } }</p>
        { % endif % }
    </form>

    < script type="text/javascript" src='{ % static "js/jquery.js" % }'></script>
    < script type="text/javascript">
    $('.captcha-refresh').click(function(){
        form = $(this).parents('form');
        $.getJSON('/captcha/refresh/', {}, function(json) {
            $('.captcha').attr('url',json.image_url);
            $('.captcha').attr('src',json.image_url);
            $('#id_captcha_0').attr('value',json.image_url.split('/')[3]);
        });
        return false;
    });
    < /script>
    </code></pre>