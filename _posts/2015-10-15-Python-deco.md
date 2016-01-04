---
layout: post
title:  "Python 装饰器"
date:   2015-10-15 19:43:42
categories: Python
excerpt:Python 装饰器
---

* content
{:toc}


## 序

装饰器可以对一个函数、方法或者类进行加工,个人理解为一个闭包，下面就来看一下装饰器的几种使用方法

---

### 使用方法

 * 无参数装饰器

   *  相当于foo=deco(foo)

   * 使用

    <pre><code>
    def deco(func):
        print("deco")
        return func

    \@deco
    def foo():
        print("foo")
    </code></pre>

 * 装饰器有参，函数无参

   * 相当于foo = deco(argv)(foo)

   * 使用

    <pre><code>
    def deco(argv):
        def decorator(func):
            print("decorator")
            return func
        print(argv)
        return decorator

    \@deco("123")
    def foo():
        print("foo")
    </code></pre>

 * 装饰器、函数都有参

   * 相当于foo = deco(argv)(foo)(data)

   * 使用

    <pre><code>
    from functools import wraps

    def deco(argv):
        def decorator(func):
            @wraps(func)
            def wrapper(*args, **kwargs):
                return func(*args, **kwargs)
            print("decorator")
            return wrapper
        print(argv)
        return decorator

    \@deco("123")
    def foo(data):
        "this is foo"
        print("foo")
        print(data)
    </code></pre>

---

### 其他

 * 注意多个装饰器的执行顺序，应该是先执行下面的，然后是上面的。这里应先执行deco1，然后是deco2
 <pre><code>
    \@deco2
    \@deco1
 </code></pre>