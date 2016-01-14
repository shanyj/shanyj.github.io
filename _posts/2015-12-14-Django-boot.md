---
layout: post
title:  "Django django-bootstrap-toolkit"
date:   2015-12-14 20:41:25
categories: Django
excerpt: Django django-bootstrap-toolkit
---

* content
{:toc}


## 序

之前使用bootstrap的aceadmin框架对前端页面进行了美化，于是搜了下bootstrap在django里的一些应用，在这里mark一下

---

### 安装

 * 安装&配置
 <pre><code>
pip install django-bootstrap-toolkit

#在app中添加
'bootstrap_toolkit',
 </code></pre>

---

### 模板组成

 * head
 <pre><code>
 < head >
    { % bootstrap_stylesheet_tag % }
    { % bootstrap_stylesheet_tag "responsive" % }
    < style type="text/css">
        body {
            padding-top: 60px;
        }
    < /style>
    < script src="//html5shim.googlecode.com/svn/trunk/html5.js"></script>
    < script src="//ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
    { % bootstrap_javascript_tag % }
< /head >
 </code></pre>

 * 导航栏
 <pre><code>
< div class="navbar navbar-fixed-top">
    < div class="navbar-inner">
        < div class="container">
            < a class="brand" href="/">django-bootstrap-toolkit< /a>
            < ul class="nav">
                < li><a>Home</a>< /li>
                < li class="dropdown">
                    < a href="#" class="dropdown-toggle" data-toggle="dropdown">Forms< b class="caret">< /b>< /a>
                    < ul class="dropdown-menu">
                        < li>< a>Vertical< /a>< /li>
                        < li>< a>Horizontal< /a>< /li>
                        < li>< a>Inline< /a>< /li>
                        < li>< a>Search< /a>< /li>
                        < li>< a>Using template< /a>< /li>
                    < /ul>
                < /li>
                < li>< a>Formset< /a>< /li>
            < /ul>
        < /div>
    < /div>
< /div>
 </code></pre>

 * 页脚
 <pre><code>
< div class="container">
    { % bootstrap_messages % }
    < footer class="row">
        < div class="span6">
            < p>This is < a href="https://github.com/dyve/django-bootstrap-toolkit">django-bootstrap-toolkit< /a>< /p>
        < /div>
        < div class="span6" style="text-align:right">
            < p>
                &copy; < a href="http://twitter.com/dyve">Dylan Verheul< /a> 2012
                |
                < a href="https://raw.github.com/dyve/django-bootstrap-toolkit/master/LICENSE">license< /a>
            < /p>
        < /div>
    < /footer>
< /div>
 </code></pre>


---

### 处理model

 * 普通方法
 <pre><code>
def hello(request):
    form=TestForm（）
    layout = request.GET.get('layout', '')
    if layout != 'search':
        layout = 'inline'
    return render_to_response('hello.html',RequestContext(request, {
        'form': form,
        'layout': 'inline',
    }))


    < form class="form-{ { layout } }" action="" method="post">
        { % csrf_token % }
        { { form|as_bootstrap:layout } }
    < input type="submit" value="Submit" class="btn btn-primary">
    < /form>
 </code></pre>

 * 使用formset
 <pre><code>
from django.forms.formsets import formset_factory
def hello(request):
    form=formset_factory(TestForm)
    layout = request.GET.get('layout', '')
    if layout != 'search':
        layout = 'inline'
    return render_to_response('hello.html',RequestContext(request, {
        'form': form,
        'layout': 'inline',
    }))

   < form class="form-{ { layout } }" action="" method="post">
        { % csrf_token % }
        { % bootstrap_formset formset=form layout=layout % }
        < input type="submit" value="Submit" class="btn btn-primary">
    < /form>
 </code></pre>


---

### 分页

 * 方法
 <pre><code>
def demo_pagination(request):
    lines = []
    for i in range(10000):
        lines.append(u'Line %s' % (i + 1))
    paginator = Paginator(lines, 10)
    page = request.GET.get('page')
    try:
        show_lines = paginator.page(page)
    except PageNotAnInteger:
        show_lines = paginator.page(1)
    except EmptyPage:
        show_lines = paginator.page(paginator.num_pages)
    return render_to_response('pagination.html', RequestContext(request, {
        'lines': show_lines,
    }))
 </code></pre>

 * 模板
 <pre><code>
 < table class="table">
        { % for line in lines % }
            < tr>
                < td>{ { line } }< /td>
            < /tr>
        { % endfor % }
< /table>
{ { lines|pagination } }
{ % bootstrap_pagination lines url="/pagination?page=1&flop=flip" extra="q=foo" size="mini" align="right" % }
{ % bootstrap_pagination lines url="/pagination?page=1" align="center" size="large" % }
 </code></pre>