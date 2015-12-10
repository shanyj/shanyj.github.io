---
layout: post
title:  "Django 安全"
date:   2015-05-17 21:02:22
categories: Django
excerpt: Django 安全
---

* content
{:toc}


## 序

安全是在进行项目开发时需要处处注意的问题，下面就总结下Django常见的安全问题的解决方法

---

### sql注入

 * 为了防止sql注入，Django总是对输入进行自动转义

 * 当直接使用sql语句时，注意使用sql占位符

    <pre><code>
    def user_contacts(request):
        user = request.GET['username']
        sql = "SELECT * FROM user_contacts WHERE username = %s"
        cursor = connection.cursor()
        cursor.execute(sql, [user])
    </code></pre>

   * execute有第二个参数，包含若干占位符，来对sql语句中的占位符赋值，过程中会自动转义


---

### 跨站点脚本xss

 * 在url中进行修改，比如http://example.com/hello/?name=<i>Jacob</i>  会返回<h1>Hello, <i>Jacob</i>!</h1>, 因此可以在url中写上jquery语句来操作数据库

 * 为此，django总是自动转义

---

### 伪造跨站点请求csrf

 * 在登陆信任网站且没退出时访问危险网站，导致cookie被盗用,比如在危险网站中插入　<img src=http://www.mybank.com/Transfer.php?toBankId=11&money=1000> 这个get／post请求，只要点击银行账户就少钱了！

 * 为此，django添加了csrf中间键

     * 只针对post请求，get请求需要设计者自己保证没有副作用

     * django.contrib.csrf.middleware.CsrfMiddleware放在中间件中，且在session之后在 GZipMiddleware 之前

     * 只有 Content‐Type头部标记为text/html ，application/xml+xhtml的页面才会被保护

     * 向所有post表单增加隐藏字段，名称为csrfmiddlewaretoken，值为当前会话id加秘钥

     * 对传入的post检查csrfmiddlewaretoken，如不正确或不存在则403

     * 仅限于常规方法创建的表单

---

### 邮件头部注入

 * 在构建邮件sunject时加上hello\ncc:spamvictim@example.com"（\\n是换行符）
    就会变成:

    To: hardcoded@example.com Subject: hello

    cc: spamvictim@example.com

    从而利用了别人的邮箱来发送了信息

 * 为此，django自带的django.core.mail 中不允许出现换行符,且总是自动转义

---

### 暴露错误消息

 * Django的出错页面总是很详尽的列出出错的原因和代码，容易将内部代码很容易的暴露出去

 * 关闭debug

---

### 伪造会话

  * 中间人攻击：监听所有网络

  * 伪造会话：利用会话id将自己伪造成用户

    * 解决：django会话id存在哈西表中，且用户去访问不存在的会话id时会生成新会话id）

  * 伪造cookie和会话滞留：获取url中的session来进行盗用

    * 解决：首先不要再url中包含session（django自带，只有映射用的session id），不在cookie存数据