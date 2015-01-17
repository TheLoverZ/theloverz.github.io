---
layout: post
title: "Rails Guides 阅读计划：Getting Started"
description: ""
category: "note"
tags: ['Ruby on Rails']
---
{% include JB/setup %}

# Rails Guides 阅读计划：Getting Started

作者：[TheLover_Z](http://theloverz.me)

最近闲着没事儿，打算把 Rails Guides 
翻出来全读一遍，然后记录一下之前忽视掉或者不知道的地方。并不打算总结，总结的话就是 Guides 的翻译了……

## `render` 和 `redirect_to` 的区别

Guides 里面说到，`render` 是作为 Form 传输请求过程的一份子，而 `redirect_to` 则会另开一个 request.

然后在 [Layouts and Rendering in Rails](http://guides.rubyonrails.org/layouts_and_rendering.html) 里面再次提到："The redirect_to method does something completely different: it tells the browser to send a new request for a different URL." 然后举的例子也提到了，在任何地方你都可以通过 `redirect_to photos_url` 来跳转到一个完全不同的 url，而 `render` 不能。

## `pluralize` 方法

`pluralize` 这个方法炒鸡萌啊- - 

    > rails c
    Loading development environment (Rails 4.1.0)
    2.0.0-p451 :001 > "apple".pluralize
     => "apples"
    2.0.0-p451 :002 > "sheep".pluralize
     => "sheep"
    2.0.0-p451 :003 > "mouse".pluralize
     => "mice"
    2.0.0-p451 :004 > "news".pluralize
     => "news"
    2.0.0-p451 :005 >

这个方法用在默认生成的 view 里面的错误提示，1 error 或者 2 errors 这样。

这么萌的方法，然后我就开开心心地去翻源码了~~（不好意思今天忘了吃药…~~

源码是这么写的：

    def pluralize(count = nil, locale = :en)
      locale = count if count.is_a?(Symbol)
      if count == 1
        self.dup
      else
        ActiveSupport::Inflector.pluralize(self, locale) # <-- 这里
      end
    end

因为 `count` 肯定不是 1 所以跳转到 `ActiveSupport::Inflector`：

    def pluralize(word, locale = :en)
      apply_inflections(word, inflections(locale).plurals) # <-- 这里
    end

先看 `inflections`：

    def inflections(locale = :en)
      if block_given?
        yield Inflections.instance(locale)
      else
        Inflections.instance(locale) # <-- 这里
      end
    end

这里跳转到了 `Inflections.instance(locale)`，而 `Inflections` 大概是这样的：

    class Inflections
      def self.instance(locale = :en)
        @__instance__[locale] ||= new
      end
    end

然后再看 `apply_inflections`：

    # Applies inflection rules for +singularize+ and +pluralize+.
    #
    #  apply_inflections('post', inflections.plurals)    # => "posts"
    #  apply_inflections('posts', inflections.singulars) # => "post"
    def apply_inflections(word, rules)
      result = word.to_s.dup

      if word.empty? || inflections.uncountables.include?(result.downcase[/\b\w+\Z/])
        result
      else
        rules.each { |(rule, replacement)| break if result.sub!(rule, replacement) } # <-- 这里
        result
      end
    end

然后在 `activesupport/lib/active_support/inflections.rb` 可以看到单词变换规则，大概像是下面的样子：

    module ActiveSupport
      Inflector.inflections(:en) do |inflect|
        inflect.plural(/$/, 's')
        inflect.plural(/s$/i, 's')

        inflect.singular(/^(a)x[ie]s$/i, '\1xis')
        inflect.singular(/(alias|status)(es)?$/i, '\1')

        inflect.irregular('person', 'people')
        inflect.irregular('man', 'men')

        inflect.uncountable(%w(equipment information rice money species series fish sheep jeans police))
      end
    end

这样很多很多的单词变形规则。

## `link_to` method

当需要删除的时候，我们使用 `link_to 'Destroy', article_path(article), method: :delete, data: { confirm: 'Are you sure?' }` 这样的代码。

其中 `data: { confirm: 'Are you sure?' }` 会使用 HTML 5 的 `data-confirm` 来提示用户是否要真的删除。这个在 `jquery_ujs` 中定义，它在 `application.js` 中被 `//= require jquery_ujs` 引用。

## nested routes

`form_for([@article, @article.comments.build])` 可以生成类似 `/articles/1/comments` 的 nested routes.

## `dependent:` keyword

假设我们有两个 Model，一个是 Article 一个是 Comment. 对应关系是 Article `has_many` Comments.

然后我们需要删掉某个 Article，但是这样的话 Comment 就会留下来成为冗余数据，这样可以使用：

    has_many :comments, dependent: :destroy

就不用手动删除了。这个在 [Associations](http://guides.rubyonrails.org/association_basics.html) 也有讲解，这里就不多说了，毕竟只是一个 Getting Started.

## `http_basic_authenticate_with` method

这个好像用得已经不多了，就是给某些 Controller 加上 Basic Auth 权限。

用法：

    http_basic_authenticate_with name: "dhh", password: "secret", except: [:index, :show]

    http_basic_authenticate_with name: "dhh", password: "secret", only: :destroy

