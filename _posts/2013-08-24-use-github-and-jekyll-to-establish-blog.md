---
layout: post
title: "用 github+jekyll 搭建个人 blog"
description: ""
category: 'tutorials'
tags: ['github', 'jekyll']
---
{% include JB/setup %}

# github+jekyll 教程

作者：[TheLover_Z](http://theloverz.me)

## 为什么要用 github+jekyll

> 「但是两年前，情况出现变化，一些程序员开始在 github 网站上搭建 blog. 他们既拥有绝对管理权，又享受 github 带来的便利----不管何时何地，只要向主机提交 commit，就能发布新文章。更妙的是，这一切还是免费的，github 提供无限流量，世界各地都有理想的访问速度。」

来源：[阮一峰的网络日志](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)

## 网上的教程一搜一大把，为什么要重复造轮子

我学过 git(github), Ruby on Rails, jekyll, markdown, 我翻了 Google 两三页的中英文内容，仍然花了一个下午加半个晚上才配置好。有的教程有误导性甚至是错的，有的教程很复杂，看起来就头晕。

## 流程

这里我会尽量详细地记录，但是有一些内容希望您能自己解决，放心，不会很难的：）

注册 [github](https://github.com/)，安装 [ruby](http://www.ruby-lang.org/zh_cn/), [gem](http://rubygems.org/) 和 [git](http://git-scm.com/). 设置好 [github ssh key](https://www.google.com.hk/search?q=github+%E8%AE%BE%E7%BD%AE+ssh+key&oq=github+%E8%AE%BE%E7%BD%AE+ssh+key&aqs=chrome..69i57j69i64.3363j0&sourceid=chrome&ie=UTF-8) 如下我列出了我这里的 ruby 和 gem 的版本号。

    > ruby -v
    ruby 1.9.3p448 (2013-06-27 revision 41675) [x86_64-darwin12.4.0]
    
    > gem -v
    1.8.25

进入 github, 点击右边中间的绿色按钮 new repository，然后可以看到下面的界面，repository name 填写你自己的 xxx.github.com（注意 github 后来改成了 github.io 但是 .com 也是可以的，其实没什么影响）

注意：如下的 `example` 均替换为您的 repository name.

![new repository](/images/1/1.png)

这里除了 repository name 以外什么都不用管，点击提交以后会跳转到下图。

![repository](/images/1/2.png)

还是什么都不用做，点击右边最后一个按钮 ![admin](/images/1/3.png)，然后会跳转到新页面，翻小半页看到下面的内容：

![pages](/images/1/4.png)

点击 Automatic Page Generator，仍然是什么都不用管，点击最下面右边的 Continue to Layouts.

然后就是选择主题了，其实这里可以随便选，因为后面要删掉的……（我试了试能不能把这个 theme 移植过去可惜失败了…），然后点绿色对号。

![theme](/images/1/5.png)

在新页面右下角找到 SSH clone URL，复制里面的地址，然后在终端运行 `git clone git@github.com:TheLoverZ/example.git` 后面的按照您自己的情况填写。

    > git clone git@github.com:TheLoverZ/example.git
    Cloning into 'example'...
    remote: Counting objects: 15, done.
    remote: Compressing objects: 100% (14/14), done.
    remote: Total 15 (delta 4), reused 0 (delta 0)
    Receiving objects: 100% (15/15), 22.51 KiB | 3 KiB/s, done.
    Resolving deltas: 100% (4/4), done.

然后

    > cd example
    > jekyll serve --watch

这里的 `--watch` 参数可以让你在修改代码的时候不需要重启服务器。这时候在浏览器中打开 http://127.0.0.1:4000 就可以看到如下页面了。

![example page](/images/1/6.png)

然后运行如下命令：

    > cd ..
    > git clone https://github.com/plusjade/jekyll-bootstrap.git
    Cloning into 'jekyll-bootstrap'...
    remote: Counting objects: 1862, done.
    remote: Compressing objects: 100% (986/986), done.
    remote: Total 1862 (delta 876), reused 1716 (delta 763)
    Receiving objects: 100% (1862/1862), 544.83 KiB | 7 KiB/s, done.
    Resolving deltas: 100% (876/876), done.

然后把 jekyll-bootstrap 的 git 信息删掉：

    > cd jekyll-bootstrap
    > rm -rf ./.git/

然后把 jekyll-bootstrap 里面的内容全都拷贝到 example 里面。

再次运行（如果你之前已经 Ctrl+C 终止掉的话） `jekyll serve --watch` 然后再次打开 http://127.0.0.1:4000 查看，好像没什么变化。

![home page](/images/1/7.png)

然后修改 `example` 目录下的 `_config.yml`.

    title : TheLover_Z
    tagline: Site Tagline
    author :
      name : TheLover_Z
      email : sethverlo5@gmail.com
      github : TheLoverZ
      twitter : TheLover_Z
      feedburner : feedname

里面的内容根据个人情况来进行填写。

如果您不喜欢 xxx.github.io 这个域名的话可以在 `example` 目录下创建一个 `CNAME` 文件，内容就一行就是您的域名。比如说我的就是：

    > cat CNAME
    theloverz.me

如果绑定的是顶级域名，则 DNS 要新建一条 A 记录到 204.232.175.78. 如何设置请参见 [Google](https://www.google.com.hk/search?q=cname+a+%E8%AE%B0%E5%BD%95%E6%80%8E%E4%B9%88%E8%AE%BE%E7%BD%AE&oq=cname+a+%E8%AE%B0%E5%BD%95%E6%80%8E%E4%B9%88%E8%AE%BE%E7%BD%AE&aqs=chrome..69i57.5951j0&sourceid=chrome&ie=UTF-8).

接下来删掉根目录下的 `index.html`, 并且删掉 `_posts` 下面的 `core-samples` 目录。

然后修改根目录下的 `index.md` 使得只有下面几行：

    ---
    layout: page
    title: Hello World!
    tagline: Supporting tagline
    ---
    {% include JB/setup %}

    <ul class="posts">
      {% for post in site.posts %}
        <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
      {% endfor %}
    </ul>


然后刷新，就可以看到……这个……效果……了……（个人 js/css 目前比较弱，试了半个小时都改不出完美效果……）

![new homepage](/images/1/8.png)

（一脸无辜……）

好啦好啦，我们来看看新建 post，运行 `rake post title="Hello World"` 就可以了，当然 title 文字根据情况自己进行变化。

然后可以在 ./_posts/2013-08-24-hello-world.md 找到这篇 post，修改如下：

    ---
    layout: post
    title: "Hello World"
    description: "first post"
    category: 'test'
    tags: ['hello', 'jekyll']
    ---
    {% include JB/setup %}

    # Hello World

    ## First post in github+jekyll

这里的 category tags 不需要解释吧…刷新看看主页，可以看到：

![homepage](/images/1/9.png)

点进去可以看到：

![first post](/images/1/10.png)

你还需要如下命令推送到 github.

    > git add .
    > git commit -m "first post"
    > git push origin

需要评论功能的话请自行搜索 disqus.

需要新建页面的话（比如「关于」）可以使用 `rake page name="pages/about"` 命令。

什么？你说那个丑到爆的主题？哦，可以这样。（擦汗……

先运行 `rake theme:install git="https://github.com/jekyllbootstrap/theme-the-program.git"` 最后会提示你 Want to switch themes now? [y/n] ，可以先按 n.

然后可以在 [Theme Explorer](http://themes.jekyllbootstrap.com/) 找到你喜欢的以后点击下面的 Install Theme 会弹出一个窗口，大概是这样的：

![theme install](/images/1/11.png)

按照它提示的输入：

    rake theme:install git="https://github.com/jekyllbootstrap/theme-the-minimum.git"

最后运行 `rake theme:switch name="the-minimum"` 就可以了。

当当当当！结果如下！

![final page 1](/images/1/12.png)

![final page 2](/images/1/13.png)

最后不要忘了：

    > git add .
    > git commit -m "theme changed."
    > git push origin

推送到服务器上。

## 最后

如果有遗漏或者什么非预期结果可以自行 Google 或者 [发邮件](mailto:sethverlo5@gmail.com) 给我。