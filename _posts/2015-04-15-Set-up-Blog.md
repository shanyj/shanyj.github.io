---
layout: post
title:  "使用Github＋jekyll搭建Blog"
date:   2015-04-15 19:17:45
categories: 杂谈
excerpt: Github＋jekyll＋markdown搭建Blog。
---

* content
{:toc}


## 序


学习过程中，存储在本地的资料、随笔越来越多，碰巧今日无事，便萌生了搭建个blog。早就听闻Github＋jekyll功能强大，埋头研究了一下午终于撘了起来，
具体过程如下。

---

### 安装ruby

* 通过 apt-get 安装ruby

<pre><code>
sudo apt-get update
sudo apt-get install ruby
</code></pre>

* 查看ruby版本

<pre><code>
ruby -v
</code></pre>

---

### 安装gem

* 通过 apt-get 安装gem

<pre><code>
sudo apt-get install ruby-gems
</code></pre>

* **更改gem源** : 在安装gem的过程中会出现找不到资源的error，我们需要从另外一个gem服务器下载安装。

<pre><code>
gem source -r https://rubygems.org/
gem source -a https://ruby.taobao.org
</code></pre>

* **查看gem源** : 使用以下命令查看gem源是否更改成功。

<pre><code>
gem sources -l
</code></pre>

---

### 安装jekyll

- **安装jekyll** :

<pre><code>
gem install jekyll
</code></pre>

- **安装rdiscount** : 这个是用来解析Markdown标记的解析包。

 <pre><code>
gem install rdiscount
</code></pre>

---

### 本地编写markdown

* 一定要确保你的文章要保存为UTF-8 无 BOM 格式才行。 文件名称不能是中文。

* 当然也可以去Jekyll themes等网站下载已有模版进行改写。


---

### 本地启动博客

* 编译md文件，启动博客：

<pre><code>
jekyll serve
</code></pre>

* 访问http://127.0.0.1:4000 , 即可查看blog效果。


---

### 推送至Git

* 在Git上创建新项目，具体步骤此处不再赘述。

* 将本地工程推送至Git

  * mkdir新文件夹,将工程移入,执行下列语句。

    <pre><code>
    git init
    git add .
    git commit -m "up"
    git remote add origin git@github.com:shanyj/shanyj.github.io.git
    git push -u origin master
    </code></pre>

---

### 访问Blog

* 以此项目为例，直接访问shanyj.github.io即可。

---

### 遇到的问题

* 从jekyll themes上clone的代码，大部分需要在执行 jekyll serve 前执行 bundle install

  * bundle安装：
    <pre><code>
    gem install bundle
    </code></pre>
  * 执行：
    <pre><code>
    bundle install
    </code></pre>
