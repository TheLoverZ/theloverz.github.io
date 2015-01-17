---
layout: post
title: "Rails Guides - Action View Overview"
description: ""
category: "note"
tags: ['Ruby on Rails']
---
{% include JB/setup %}

# Action View Overview 笔记

最近翻出来 5 月的时候做的 Rails Guides 笔记，才想起来后来就没再写 Blog 了…最近一段时间尽量给补全…

## action pack

在 Rails 中，web 请求通过 Action Pack 来处理，逻辑部分交给 Action Controller，视图渲染部分交给  Action View.

最终的 HTML 由三部分构成：templates, partials 和 layouts.

## partial

partial templates 简称 partial，为了和普通的 view 区分，partial templates 文件名前有下划线。

在 `render` 的时候可以使用 `as` 来指定一个不同的名字，比如说：

    <%= render partial: "product", as: "item" %>

使用 `object` 可以很方便地指定要传递给 `partial` 的对象，比如：

    <%= render partial: "product", locals: {product: @item} %>

可以简写成：

    <%= render partial: "product", object: @item %>

但个人还是更喜欢显式传递。

集合也可以 `render`，假如说我们有：

    <% @products.each do |product| %>
      <%= render partial: "product", locals: { product: product } %>
    <% end %>

就可以一行流： 

    <%= render partial: "product", collection: @products %>

什么还想更简单？

    <%= render @products %>

这样就好了。

## partial layouts

partial layout 简单来说，就是把一部分模板嵌入另一个模板。

假如说有 `posts/_box.html.erb` 这么一个文件，内容是：

    <div class='box'>
      <%= yield %>
    </div>

还有一个 `posts/_post.html.erb` 文件：

    <%= div_for(post) do %>
      <p><%= post.body %></p>
    <% end %>

这时候，`render partial: 'post', layout: 'box', locals: {post: @post}` 就会把 `post` 嵌套在 `box` 中，结果如下：

    <div class='box'>
      <div id='post_1'>
        <p>Partial Layouts are cool!</p>
      </div>
    </div>

## action view helpers

### RecordTagHelper

RecordTagHelper 包括 `content_tag_for` 和 `div_for`.

`content_tag_for` 我觉得最有用的一点就是可以给 HTML 元素加 class，比如说官方的这个例子：

    <%= content_tag_for(:tr, @post, class: "frontpage") do %>
      <td><%= @post.title %></td>
    <% end %>

会生成：

    <tr id="post_1234" class="post frontpage">
      <td>Hello World!</td>
    </tr>

当然，和大多数 rails methods 尿性一样，这个东西可以传 collection：

    <%= content_tag_for(:tr, @posts) do |post| %>
      <td><%= post.title %></td>
    <% end %>

会生成

    <tr id="post_1234" class="post">
      <td>Hello World!</td>
    </tr>
    <tr id="post_1235" class="post">
      <td>Ruby on Rails Rocks!</td>
    </tr>

而 `div_for` 其实就是第一个参数是 `div` 的 `content_tag_for`…

看源码就明白了：

    def div_for(record, *args, &block)
      content_tag_for(:div, record, *args, &block)
    end

### AssetTagHelper

AssetTagHelper 最常用的应该是 `image_tag`，但你是否知道在 `config.action_controller.asset_host` 可以看到 assets 根目录？

`register_javascript_expansion` 和 `register_stylesheet_expansion` 的作用是用类似 bundle 的方式把该加载的 `js` 和 `css` 给加载了，拿 `register_javascript_expansion` 来说。

    ActionView::Helpers::AssetTagHelper.register_javascript_expansion monkey: ["head", "body", "tail"]

然后

    javascript_include_tag :monkey

会生成：

    <script src="/assets/head.js"></script>
    <script src="/assets/body.js"></script>
    <script src="/assets/tail.js"></script>

`image_path` 和 `image_url` 的区别就在于绝对和相对路径了，与此类似的还有 `javascript_path` 和 `javascript_url` 等。

    image_path("edit.png") # => /assets/edit.png

    image_url("edit.png") # => http://www.example.com/assets/edit.png

而 `image_tag` 是用来生成 `<img>` 标签的。

    image_tag("icon.png") # => <img src="/assets/icon.png" alt="Icon" />

### AtomFeedHelper

就是用来生成 Atom 的，官方 Guides 有很详细的例子没什么好说的。

### BenchmarkHelper

性能测试 helper.

### CaptureHelper

`content_for` 就是在一个地方写类似这样的代码：

    <% content_for :special_script do %>
      <script>alert('Hello!')</script>
    <% end %>

然后在另一个地方

    <%= yield :special_script %>

就可以嵌入代码。

### DebugHelper

这个比较好玩，比如说

    my_hash = {'first' => 1, 'second' => 'two', 'third' => [1,2,3]}
    debug(my_hash)

可以生成

    <pre class='debug_dump'>---
    first: 1
    second: two
    third:
    - 1
    - 2
    - 3
    </pre>

debug 比较方便。

### SanitizeHelper

这个主要是用在 `sanitize` 方法上。可以设置白名单：

    class Application < Rails::Application
      config.action_view.sanitized_allowed_tags = 'table', 'tr', 'td'
    end

不符合条件的都会被过滤掉。
