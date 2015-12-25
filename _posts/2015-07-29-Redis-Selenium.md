---
layout: post
title:  "Python "
date:   2015-07-27 21:18:28
categories: Python
excerpt: Python
---

* content
{:toc}


## 序

在python中调用redis十分easy，因为几乎大部分命令都与原生redis命令类似，但也有诸如pipeline和订阅等命令需要着重关注

---

### 简单示例

 * 示例

   * 对于不同类型的webdriver需要安装不同的webdriver
    <pre><code>
    from selenium import webdriver
    driver =webdriver.Chrome()
    driver.get('http://radar.kuaibo.com')
    print driver.title
    driver.quit()
    </code></pre>
    
---

### 元素定位

 * id:

    * browser.find_element_by_id( "kw" )

 * name

    * browser.find_element_by_name( "wd" )

 * class name

    * browser.find_element_by_class_name( "s_ipt" )

 * link text

    * 有时候不是一个输入框也不是一个按钮,而是一个文字链接,我们可以通过 link

    * browser.find_element_by_link_text("贴 吧").click()

 * partial link text

    * browser.find_element_by_partial_link_text("贴")

 * tag name

    *browser.find_element_by_tag_name( "input" )

 * xpath

    * browser.find_element_by_xpath( "//input[@id='kw']" )

    * browser.find_element_by_xpath("//tr[@id='check']/td[2]")

    * driver.find_element_by_xpath("//tr[7]/td[2]")

    * driver.find_element_by_xpath("//div[@id='fm']/form/span/input")

    * driver.find_element_by_xpath("//a[contains(text(),'网页')]")

 * css selector

    * browser.find_element_by_css_selector( "#kw" )

    * browser.find_element_by_css_selector("a[name='tj_news']")

    * driver.find_element_by_css_selector("a.RecycleBin")

---

### 休眠

 *
     <pre><code>
    import time
    time.sleep(3)
    </code></pre>

 * implicitly_wait() :

    * 方法就可以方便的实现智能等待，可以在一个时间范围内智能的等待。
隐式地等待一个无素被发现或一个命令完成;这个方法每次会话只需要调用一次

    * browser.implicitly_wait(30)

---

### 元素操作

 * click:

    * 点击对象

 * send_keys:

    * 在对象上模拟按键输入

 * clear:

    * 清除对象的内容,如果可以的话，用于清除输入框的内容,比如百度输入框里默认有个“请输入关键
字”的信息,再比如我们的登陆框一般默认会有“账号”“密码”这样的默认信息

 * submit:

    * 清除对象的内容,如果可以的话
---

### 键盘&鼠标事件

 * 键盘组合事件:

    * .send_keys(Keys.CONTROL,'a')

    * 相当于ctrl+a

 * 上传文件:

    * send_keys('D:\\selenium_use_case\upload_file.txt')

 * 右击:

    * ActionChains(driver).context_click(qqq).perform()

 * 双击:

    * ActionChains(driver).double_click(qqq).perform()

 * 移动:

    * ActionChains(driver).drag_and_drop(element, target).perform()


---

### 框架/窗口定位

 * browser.switch_to_frame("f1") 跳到指定frame

 * driver.switch_to_window("windowName") 跳到指定window

 * driver.switch_to_alert().accept() 跳到弹出框并点击确定

---

### js

 * 执行 js 一般有两种,
一种是在页面上直接执行 JS,
另一种是在某个已经定位的元素上执行 JS

 * 第一种：driver.execute_script('$("#tooltip").fadeOut();')

 * 第二种：
     <pre><code>
    button = driver.find_element_by_class_name('btn')
    driver.execute_script('$(arguments[0]).fadeOut()',button)
     </code></pre>