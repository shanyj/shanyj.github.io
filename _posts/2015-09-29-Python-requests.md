---
layout: post
title:  "Python Requssts模块"
date:   2015-09-29 21:02:18
categories: Python
excerpt: Django Requssts模块
---

* content
{:toc}


## 序

python的requests模块是一个HTTP客户端库,跟urlli,urlli2类似，这里就讲解一下它的大致用法

---

### 安装

* 安装

    <pre><code>
    pip install requests
    </code></pre>

---

### 基本用法

 * get方法：r = requests.get('https://api.github.com/events')

 * post方法：r = requests.post("http://httpbin.org/post", data = {"key":"value"})

 * put方法：r = requests.put("http://httpbin.org/put", data = {"key":"value"})

 * delete方法：r = requests.delete("http://httpbin.org/delete")

 * head方法：r = requests.head("http://httpbin.org/get")

 * options方法：r = requests.options("http://httpbin.org/get")

---

### data&params

 * data是传输的参数

 * params是用？追加到url后的
    <pre><code>
    >>> payload = {'key1': 'value1', 'key2': ['value2', 'value3']}
    >>> r = requests.get("http://httpbin.org/get", params=payload)
    >>> print(r.url)
    http://httpbin.org/get?key1=value1&key2=value2&key2=value3
    </code></pre>

---

### response处理

 * r = requests.post("http://httpbin.org/post", data = {"key":"value"})

 * 查看url： print r.url

 * 查看普通返回结果：r.text

 * 查看二进制返回结果：r.content

 * json形式：r.json(),注意有括号

 * 查看返回码：r.status_code

 * 查看headers：r.headers

 * 查看cookie：r.cookies['example_cookie_name']

---

### 添加herder

 * 相当与curl命令的-H
     <pre><code>
    >>> url = 'https://api.github.com/some/endpoint'
    >>> headers = {'user-agent': 'my-app/0.0.1'}
    >>> r = requests.get(url, headers=headers)
    </code></pre>

---

### post文件

 * files的第一个参数文件名，第二个是文件，第三个是headers

    <pre><code>
    >>> url = 'http://httpbin.org/post'
    >>> files = {'file': ('report.xls', open('report.xls', 'rb'), 'application/vnd.ms-excel', {'Expires': '0'})}
    >>> r = requests.post(url, files=files)
    </code></pre>

---

### 添加cookie&过期时间

 * cookies
 <pre><code>
    >>> url = 'http://httpbin.org/cookies'
    >>> cookies = dict(cookies_are='working')
    >>> r = requests.get(url, cookies=cookies)
 </code></pre>

 * 过期时间：requests.get('http://github.com', timeout=0.001)

---

### 使用session

 * 访问过程使用session
 <pre><code>
    url = r'http://www.renren.com/ajaxLogin'
    user = {'email':'email','password':'pass'}
    s = requests.Session()
    r = s.post(url,data = user)
 </code></pre>

 * 使用session验证
 <pre><code>
    s = requests.Session()
    s.auth = ('user', 'pass')
    s.headers.update({'x-test': 'true'})
    s.get('http://httpbin.org/headers', headers={'x-test2': 'true'})
 </code></pre>

---

### 下载文件

 * 下载文件
  <pre><code>
url = 'http://www.pythontab.com/test/demo.zip'
r = requests.get(url)
with open("demo3.zip", "wb") as code:
     code.write(r.content)
  </code></pre>