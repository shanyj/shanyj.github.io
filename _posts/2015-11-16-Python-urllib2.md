---
layout: post
title:  "Python Urllib2模块"
date:   2015-11-16 21:03:16
categories: Python
excerpt: Python Urllib2模块
---

* content
{:toc}


## 序

urllib2是一个HTTP 客户端库。这里总结了一些 urllib2 库的使用细节

---

### urllib2

 * 调用
 <pre><code>
    import urllib2
    response=urllib2.urlopen('http://www.douban.com')
    html=response.read()
 </code></pre>

 * 提交数据
 <pre><code>
    import urllib
    import urllib2
    url = 'http://shanyj.github.io'
    info = {'name' : 'syj'',
              'location' : 'shanghai'}
    data = urllib.urlencode(info)  #info 需要被编码为urllib2能理解的格式，这里用到的是urllib
    req = urllib2.Request(url, data)
    response = urllib2.urlopen(req)
    the_page = response.read()
 </code></pre>

 * headers
 <pre><code>
    url = 'http://sssss'
    user_agent = 'Mozilla/4.0 (compatible; MSIE 5.5; Windows NT)'# 将user_agent写入头信息
    values = {'name' : 'syj',
              'location' : 'sh',
              'language' : 'Python' }
    headers = { 'User-Agent' : user_agent }
    data = urllib.urlencode(values)
    req = urllib2.Request(url, data, headers)
    response = urllib2.urlopen(req)
    the_page = response.read()
 </code></pre>

 * timeout
 <pre><code>
    import urllib2
    response = urllib2.urlopen('http://www.google.com', timeout=10)
    response.geturl()  ＃获取访问后的url 以查看是否重定向
 </code></pre>

 * 禁止重定向
 <pre><code>
    class RedirectHandler(urllib2.HTTPRedirectHandler):
        def http_error_301(self, req, fp, code, msg, headers):
            pass
        def http_error_302(self, req, fp, code, msg, headers):
            pass
    opener = urllib2.build_opener(RedirectHandler)
    opener.open('http://www.google.cn')
 </code></pre>

 * cookie
 <pre><code>
    cookie = cookielib.CookieJar()
    opener = urllib2.build_opener(urllib2.HTTPCookieProcessor(cookie))
    response = opener.open('http://www.google.com')
    for item in cookie:
        if item.name == 'some_cookie_item_name':
            print item.value
 </code></pre>

 * PUT 和 DELETE 方法
 <pre><code>
    request = urllib2.Request(uri, data=data)
    request.get_method = lambda: 'PUT' # or 'DELETE'
    response = urllib2.urlopen(request)
 </code></pre>

 * http返回码
 <pre><code>
    try:
        response = urllib2.urlopen('http://restrict.web.com')
    except urllib2.HTTPError, e:
        print e.code
 </code></pre>