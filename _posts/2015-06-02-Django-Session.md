---
layout: post
title:  "Django Session&Cookie"
date:   2015-06-02 19:51:34
categories: Django
excerpt: Django Session&Cookie
---

* content
{:toc}


## 序

Django的session框架可以存储和获取访问者的数据信息，并将信息保存在服务器上，同时以 cookies 的方式发送和获取一个包含 session ID的值

---

### cookie

 * 存cookie：

    <pre><code>
   def cookiew(request):
        response=HttpResponse('aa')
        response.set_cookie('favorite_color','aaaaa')
        return response
    </code></pre>

 * 取cookie：

    <pre><code>
    def cookier(request):
        a=request.COOKIES['favorite_color']
        return HttpResponse(a)
    </code></pre>

 * 删除Cookies:

    <pre><code>
    response.delete_cookie("cookie_key",path="/",domain=name)
    </code></pre>

 * set_cookie参数：

    * max_age: cookies的持续有效时间（以秒计），如果设置为 None cookies 在浏览器关闭的时候就失效了。

    * expires: cookies的过期时间，格式： "Wdy, DD-Mth-YY HH:MM:SS GMT" 如果设置这个参数，它将覆盖 max_age 参数。

    * path: cookie生效的路径前缀，浏览器只会把cookie回传给带有该路径的页面，这样你可以避免将cookie传给站点中的其他的应用。当你的应用不处于站点顶层的时候，这个参数会非常有用。

    * domain: cookie生效的站点。你可用这个参数来构造一个跨站cookie。如domain=".example.com"所构造的cookie对下面这些站点都是可读的： www.example.com 、 www2.example.com 和an.other.sub.domain.example.com 。如果该参数设置为 None ，cookie只能由设置它的站点读取。

    * secure: 如果设置为 True ，浏览器将通过HTTPS来回传cookie。


---

### session

 * 设置session

    * django.contrib.sessions.middleware.SessionMiddleware 放入中间键中

    * django.contrib.sessions 放入app中

    * python manage.py syncdb

 * 设置session

    * request.session['haha']='syj'

 * 取session：

    * s=request.session['haha'］或 request.session.get('haha','')

 * 删除session

    * del request.session["fav_color"]

---

### 检测cookie

 * 使用session和cookie前一般需要进行检测

    <pre><code>
    def testform(request):
        if request.method=='GET' and 'name' in request.GET:
            if request.session.test_cookie_worked():
                request.session.delete_test_cookie()
                return HttpResponse('ok!')
            else:
                return HttpResponse('no cookie')
        else:
            request.session.set_test_cookie()
            form=canform()
            return render_to_response('searchnext.html',{'form':form})
    </code></pre>

---

### session操作

 * 可以通过数据库来取，s=Session.objects.get(pk='ww6xdvzwtg5qz3ysnju7754b5ypyou3a')

 * 获取属性 s.expire_date、s.session_data、s.get_decoded()

 * 默认在数据改变时才保存，可以设置SESSION_SAVE_EVERY_REQUEST 为 True，则每次读写都保存

 * 默认浏览器关闭时实效，SESSION_EXPIRE_AT_BROWSER_CLOSE 设置为 False,这样,会话cookie可以在用户浏览器中保持 SESSION_COOKIE_AGE