---
layout: post
title:  "Django pageinator 分页"
date:   2015-04-18 21:02:28
categories: Django
excerpt: Django pageinator 分页
---

* content
{:toc}


## 序


项目中显示文件列表，当文件过多时页面显示不美观，且从数据库读取数据代价很大，就要用到分页的技术，在文档系统项目中测试了下分页技术，感觉挺实用的。具体配置如下：

---

### pageinator类

* 分页用到了pageinator类，可以看一下该类的两个例子

<pre><code>
current_page = int(request.GET.get('current_page','1')
repop=Paginator(repopage,5)
＃第一个参数是要分页的序列，第二个参数是每页的条目数
try:
    repos=repop.page(current_page)
except:
    pass
</code></pre>

<pre><code>
>>> objects = ['john', 'paul', 'george', 'ringo']
>>> p = Paginator(objects, 2)
>>> p.count  //总数
4
>>> p.num_pages  //几页
2
>>> p.page_range  //页的范围
</code></pre>


---

### 类方法

* Page

一般由Paginator.page(number)生成，如Page＝Paginator(repopage,5).page(current_page)

* Page.has_next()

如果下一页存在，返回 True。

* Page.has_previous()

如果前一页存在返回 True

* Page.has_other_pages()

如果上一页面或者下一页存在，返回 True

* Page.next_page_number()

返回下一页的索引，这个函数比较傻（不管下一页是否存在，都是简单的+1）

* Page.previous_page_number()

返回上一页的索引，其他同上

* Page.start_index()

返回当前页，第一个对象的索引。

* Page.end_index()

道理同上。

---

### 显示

* 使用下列语句可以逐条显示每页中的纪录

<pre><code>
{ % for blog in repos % }
 { { blog.name } }
 { { blog.content } }
  ......
{ % endfor % }
</code></pre>

---

### 翻页

* 通过  < a > 链接跳转实现

<pre><code>
{ % if repos.has_previous % }
< a href="?word={ {word} }&current_page={ { repos.previous_page_number } }" class='paginator' title='上一页'>上一页< /a>
{ % endif % }

< a href="?word={ {word} }&current_page={ { current_page } }" class='paginator' title=''>{ {current_page} }< /a>

{ % if repos.has_next % }
< a href="?word={ {word} }&current_page={ { repos.next_page_number } }" class='paginator' title='下一页'>下一页< /a>
{ % endif % }
</code></pre>
