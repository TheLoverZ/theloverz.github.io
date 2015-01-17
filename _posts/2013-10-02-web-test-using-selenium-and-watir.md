---
layout: post
title: "Web Test Using Selenium and Watir"
description: ""
category: "tutorials"
tags: ['selenium', 'watir', 'web']
---
{% include JB/setup %}

# web 自动化测试简介之 selenium & watir

作者：[TheLover_Z](http://theloverz.me/)

## 什么是 web 自动化测试以及为什么要用（不要用） web 测试？

举个栗子，您在修改一个 web 项目，可能需要不断刷新页面来看一下效果如何。比如说 button 是否工作正常，表单提交或者注册流程是否正常。

如果页面不多的话手动测试还好，如果项目相对复杂一点的话就会很麻烦，这时候就有必要使用 web 自动化测试了。

当然，在某些情况下 web 测试也可能会给您带来额外的麻烦，包括但不限于如下情况：

- deadline 马上就要到了，这时候补上测试可能会消耗很多人力物力。
- 流程过于复杂/简单，以至于 web 测试效果不好。
- 需求可能频繁改动，这时候需要更多的精力去维护测试。

## web 自动化测试常用工具有哪些？

很抱歉我也刚接触这些不久，据我所知，比较常用的包括：

- selenium（有方便好用的 ide，支持 ie & chrome & firefox & safari）
- casperjs（js 实现，不支持 ie）
- watir（ruby 实现，语法简单好用，支持 ie & chrome & firefox & safari）

## 这么多工具我该选择哪个？

- 如果不用考虑 IE 的话可以选择 casperjs.
- 如果您对 ruby 比较熟悉的话可以选择 watir（支持 IE），毕竟 ruby 的语法糖美味至极。
- 如果您对 pyhon 比较熟悉并且要考虑 IE 的话可以考虑 selenium
- 如果您对 python / ruby 都不太熟的话可以考虑 selenium，这货支持 C# / Java，并且支持 IE

## selenium 入门

我想先拿 selenium 来举个栗子，毕竟这货比较老牌（据说 Google 都在用）。

### selenium RC / IDE / webdriver

如果您尝试着来到 [selenium 主页](http://docs.seleniumhq.org/)，一定会被 RC / IDE / webdriver 这些词语吓跑。这些是什么？！

- RC 是 Remote Control 的缩写。主要用来突破同源策略（见 [百度百科(中文)](http://baike.baidu.com/view/3747010.htm)，[维基百科(英文)](http://en.wikipedia.org/wiki/Same_origin_policy)）的限制。如果您不知道什么是同源策略，那么就可以忽略 RC 了。
- IDE 是 Firefox 的一款插件，可以通过鼠标简单点几下来录制脚本。
- webdriver 使用更简单，一般使用这个就够了。

### selenium IDE 使用教程

#### 初识 IDE

安装好 Firefox 和 selenium IDE. 以百度为例，打开 Firefox 以后在「工具」栏最下面可以看到 selenium IDE，打开以后可以看到右上角有个粉红色的点，这说明脚本已经开始录制了。

打开 www.baidu.com，点击「知道」，什么都不做，再点「网页」回来。这时候就可以看到 ide 已经录制了一定的脚本了（如图 1）。

![selenium ide](/images/web_test/1.png)

这时候可以点击粉红色的按钮，结束脚本录制。

可以看到，左上角可以通过 Fast-Slow 调节速度。右边两个绿色的按钮分别代表：执行全部脚本，执行当前脚本。

这时候点一下第二个绿色的按钮 ![执行当前脚本](/images/web_test/2.png)，执行当前脚本。可以看到 Firefox 又神奇的打开了，然后点了一下「知道」，又点了一下「网页」，就像您之前所做的那样。

估计您也能猜到，clickAndWait 意思是点击并且等待加载，这些 api 可以在[这里](http://release.seleniumhq.org/selenium-core/0.8.0/reference.html) 查到，比如说鼠标行为 `MouseMove` 和键盘行为 `KeyPress`.

值得一提的是，`link=网页` 语法表示这个动作是点击 `<a href="http://www.baidu.com/">网页</a>` 这样的链接，同理，`link=` 可以替换为 `xpath=`（关于 xpath 的语法请自行 Google），或者是 `name=` 表示 HTML 中 `name` 这样的属性，或者是 css 选择器。

#### 导出为脚本

乍一看，在 IDE 运行挺方便，其实有很多限制，比如说只能在 Firefox 下运行。这时候就需要导出脚本，我在这里以 python 为例子。

选择 File - Export Test Case As - Python2 Webdriver，当然您可以选择自己喜欢的语言。导出代码如下：

    from selenium import webdriver
    from selenium.webdriver.common.by import By
    from selenium.webdriver.support.ui import Select
    from selenium.common.exceptions import NoSuchElementException
    import unittest, time, re

    class Test(unittest.TestCase):
        def setUp(self):
            self.driver = webdriver.Firefox()
            self.driver.implicitly_wait(30)
            self.base_url = "http://www.baidu.com/"
            self.verificationErrors = []
            self.accept_next_alert = True

        def test_baidu(self):
            driver = self.driver
            driver.get(self.base_url + "/")
            driver.find_element_by_link_text("知 道").click()
            driver.find_element_by_link_text("网页").click()

        def is_element_present(self, how, what):
            try: self.driver.find_element(by=how, value=what)
            except NoSuchElementException, e: return False
            return True
    
        def is_alert_present(self):
            try: self.driver.switch_to_alert()
            except NoAlertPresentException, e: return False
            return True
    
        def close_alert_and_get_its_text(self):
            try:
                alert = self.driver.switch_to_alert()
                alert_text = alert.text
                if self.accept_next_alert:
                    alert.accept()
                else:
                    alert.dismiss()
                return alert_text
            finally: self.accept_next_alert = True
    
        def tearDown(self):
            self.driver.quit()
            self.assertEqual([], self.verificationErrors)

    if __name__ == "__main__":
        unittest.main()

值得关注的是，这里用到了 Python 的 unit test 框架，`setUp()` 和 `tearDown()` 是构造函数，分别用于在对象初始化和销毁的时候执行。

真正有用的其实就是 `test_baidu()` 这个方法，这个后面的名字会根据保存的名字而改变。

`driver.find_element_by_link_text("知 道").click()` 表示寻找对应 link text 元素，然后进行点击。API 可以在 [这里](http://selenium.googlecode.com/git/docs/api/py/genindex.html) 进行查询。或者参照我后面列出来的 cheatsheet.

#### 多浏览器支持（Python 版）

Chrome 请下载 [chromedriver](https://code.google.com/p/chromedriver/downloads/list) 然后调用 `self.driver = webdriver.Chrome('/Users/thelover_z/Desktop/selenium/chromedriver')`.

Firefox 直接调用 `self.driver = webdriver.Firefox()` 即可。

IE 可以在 [这里](https://code.google.com/p/selenium/downloads/list) 找到下载，使用方法类似 Chrome.

### cheatsheet

- `self.assertTrue()` 表示判断元素是否存在。`assert` 和 `verify` 最大的不同就在于 `verify` 结果为 false 的话并不会退出。下同。
- `assertEqual(a, b)` 判断 a 和 b 的值是否相等。
- `driver = self.driver` 初始化方法，非常常用，比如下面这两条。
- `driver.get(self.base_url + "/")` 使用 `get` 方法得到结果。
- `driver.find_element_by_link_text()` 在页面中寻找元素
- `driver.find_element_by_css_selector()` 通过 css 来查找。
- `driver.find_element_by_name()` 通过 HTML 中的 `name=` 属性来查找。
- `driver.find_element_by_id()` 通过 `id` 属性查找元素。
- `driver.find_element_by_name("username").send_keys("name")` 用于输入用户名（密码同理）。
- `driver.find_element_by_link_text("首页").click()` 模拟鼠标点击。
- `driver.maximize_window()` 最大化浏览器窗口。
- `self.driver.implicitly_wait(5)` 用于设置 timeout.
- `driver.get_window_size()['width']` 和 `driver.get_window_size()['height']` 可以得到窗口的宽和高。
- `driver.get_screenshot_as_file()` 截图。（Chromedriver 目前有 bug，不能截全图，只是当前窗口。IEDriver 和 FF 都没问题。开发者说 [正在修复这个 bug](https://code.google.com/p/chromedriver/issues/detail?id=294)）

### 常用链接

- [selenium-python (英文)](http://selenium-python.readthedocs.org/en/latest/)
- [官方文档](http://selenium.googlecode.com/git/docs/api/py/index.html)
- [官方 API](http://selenium.googlecode.com/git/docs/api/py/genindex.html)
- [多浏览器支持](http://www.shenyanchao.cn/blog/2012/10/12/selenium-multiple-browser-support/)
- [selenium 中文文档 (非官方)](https://github.com/fool2fish/selenium-doc)
- [民间 selenium 介绍 (java 版)](http://blog.csdn.net/terryzero/article/details/4523774)
- [infoQ 的 selenium 介绍](http://www.infoq.com/cn/news/2011/07/selenium-arch-2)
- [selenium webdriver / RC 区别](http://tuohuang.thoughtworkers.org/?p=157)
- [selenium 私房菜系列](http://www.ltesting.net/ceshi/open/kygncsgj/selenium/2011/1008/203310.html)


## watir 入门

### 初识

watir 使用 ruby 编写，一句 `gem install watir-webdriver` 即可安装（前提当然是需要安装 [gem](http://rubygems.org/)），然后在 `.rb` 文件中引用 `require 'watir-webdriver'` 即可。

有了上面的基础，相信 watir 更容易上手。如下的代码调用了 firefox，然后打开百度，在 `name="wd"` 的 textfield 中填写内容，然后点击 `id="su"` 的 button，输出当前 url，并且关闭浏览器。

    #coding: UTF-8
    require 'watir-webdriver'

    browser = Watir::Browser.new :firefox
    browser.goto "http://baidu.com"
    browser.text_field(:name => 'wd').set("WebDriver rocks!")
    browser.button(:id => 'su').click
    puts browser.url
    browser.close

### 多浏览器支持

下载 IEDriver 和 ChromeDriver 以后放在 gem 目录下就可以调用如下的命令来启动不同的浏览器。

    Watir::Browser.new :chrome
    Watir::Browser.new :firefox
    Watir::Browser.new :ie

### cheatsheet

- `b.driver.save_screenshot images_path` 截图，`b` 代表 `b = Watir::Browser.new :firefox`，下同。
- `b.close` 关闭浏览器。
- `b.goto "http://baidu.com"` 打开页面。
- `a.click` 点击元素（注意事项参见下条括号）。
- `a.exist?` 判断 a 元素是否存在（注意，存在并不一定可点击。比如说 CSS 的 `display: none` 属性）。
- `b.li(:xpath => "/html")` 使用 xpath 选择 *第一个* li 元素。
- `b.spans(:text => "/html")` 使用 text 选择 *所有的* span 元素。
- `b.link(:text => a)` 寻找 `<a>` 元素。
- `b.text_field(:name => 'username')` 寻找 text_field 元素。
- `b.button(:class => 'submit')` 通过 class 属性寻找 button 元素。
- `username.set "username"` 向表单输入内容。
- `b.execute_script("$('.login').css('display','block')")` 用于 CSS 属性设置为 `display: none` 导致不可点击的情况。同理，可以在页面中执行任何 js 脚本。
- `b.link(:class, "login").fire_event "onmouseover"` 模拟鼠标移到元素上的效果。
- 对于 `<div><a><span class="just_for_fun"></span></a></div>` 这样的 HTML，可以先使用 `s = b.span(:class => "just_for_fun")` 选择最里层的 span，然后通过 `s.parent.html` 可以选择到父元素（即这里的 `<a></a>`），有时候元素不好选择的时候可以用这个办法。
- `spans = b.spans()` 选择以后可以使用 `spans.each do |span|` 来逐个处理。

### 常用链接

- [实用指南](https://github.com/easonhan007/webdriver_guide/blob/master/README.md)
- [rubyforge 的文档](http://wtr.rubyforge.org/rdoc/1.6.5/)
- [watir 官方文档 (英文)](http://watirwebdriver.com/)
- [watir 官方文档 (中文)](http://www.17test.info/?page_id=469)
- [rubydocs 的 API 大全](http://rubydoc.info/gems/watir-webdriver)
- [watir cheatsheet](http://pettichord.com/watirtutorial/docs/watir_cheat_sheet/WTR/Cheat%20Sheet.html)

## FAQ

- 我发现 selenium 的文档例子很少，文档也不详细，应该怎么办？

    请搜索 stackoverflow，比如说您想搜索到鼠标双击动作如何实现，可以以 `stackoverflow selenium mouse double click` 作为关键词。基本上都可以搜到。实际上我也是靠 stackoverflow 来写的。

- 我发现 IEDriver 会经常出一些莫名其妙的问题？

    Google 关键词「IE 保护模式」

- 我想进行并行测试怎么办？

    关键词「testNg」，这是个 Java 框架……

- 我发现 Chrome 经常会弹出两个？

    这个是 ChromeDriver 的 bug…

## 特别感谢

- [zxc111](http://zxc111.net/wordpress)
- [EdgarWang](http://ruby-china.org/edgar_wang_cn)
- [NetPuter](http://netputer.me/)
- [肥魔](http://fanfou.com/Sery)
