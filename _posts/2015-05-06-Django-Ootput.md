---
layout: post
title:  "Django 输出非Html内容"
date:   2015-05-06 21:12:21
categories: Django
excerpt: Django 输出非Html内容
---

* content
{:toc}


## 序

实际项目中，通常需要输出非Html内容，比如下载页自动下载等，于是对Django输出非Html内容做了小结。

---

### StringIO

 * StringIO经常被用来作为字符串的缓存，因为StringIO有个好处，他的有些接口和文件操作是一致的，也就是说用同样的代码，可以同时当成文件操作或者StringIO操作。

    <pre><code>
    temp=StringIO()
     </code></pre>

---

### 输出PDF

 * StringIO经常被用来作为字符串的缓存，因为StringIO有个好处，他的有些接口和文件操作是一致的，也就是说用同样的代码，可以同时当成文件操作或者StringIO操作。

 * 输出非Html内容时，需要对response的mimetype和Content-Disposition进行一些修改。

 * 输出PDF需要使用canvas来进行内容写入和保存

    <pre><code>
    from reportlab.pdfgen import canvas

    def showpdf(request):
        reponse=HttpResponse(mimetype='application/pdf')
        reponse['Content-Disposition']='attachment;filename=a.pdf'

        temp=StringIO()

        p=canvas.Canvas(temp)
        p.drawString(800,800,'asdhafuhnsdfn')
        p.showPage()
        p.save()

        response.write(temp.getvalue())
        return response
     </code></pre>

---

### 输出CSV

 * 输出其他内容的方法与PDF类似，只需要进行稍微的修改即可，以CSV为例，不在使用canvas，而使用如下代码
    <pre><code>
    writer=csv.writer(temp)
    writer.writerow(['aaaaaaaaaa','bbbbbbbbbbb'])
    </code></pre>
