---
layout: post
title: "Ruby on Rails 随手笔记"
description: ""
category: "note"
tags: ['Ruby on Rails']
---
{% include JB/setup %}

# Ruby 随手笔记

作者：[TheLover_Z](http://theloverz.me)

最近折腾了一道我觉得[挺有意思的题](https://github.com/TheLoverZ/patients)，然后也是第一次从 Rails 3.2.14 折腾到 Rails 4.1.0，碰到了一些坑…随手记录一下…

## Rspec 3

别打我…但我碰到的第一个坑确实是来自 Rspec 3…

现在 `pending` 会给你算成 error 而不是从前的 pending 了，生成 scaffold 以后手贱跑了一下测试然后吓了一大跳。

现在的 `pending` 大概是这个样子（随手添加的）

    ...........................F...........................................................

    Failures:

      1) Patient methods#ages foobar FIXED
         Expected pending 'No reason given' to fail. No Error was raised.
         # ./spec/models/patient_spec.rb:93:in `block (2 levels) in <top (required)>'
         # ./spec/models/patient_spec.rb:88:in `block in <top (required)>'
         # ./spec/models/patient_spec.rb:4:in `<top (required)>'

    Finished in 2.02 seconds
    87 examples, 1 failure

    Failed examples:

    rspec ./spec/models/patient_spec.rb:93 # Patient methods#ages foobar

    Randomized with seed 24540

而且还有一些语法的变化，比如说 `.should` 语法不允许再用了（当然可以修改配置），统一都是 `expect()` 语法。

## Raill 启动过程

[题目中](https://github.com/TheLoverZ/patients/blob/master/README.md) 提到了一句: "When running the application it shall automatically create two locations: 'Test location 1' and 'Test location 2' which can be used for testing the web site."，意思就是要初始化两个测试数据。很自然地就想到了 `config/initializers/foo.rb` 这样的形式，最初的时候写的直接就是：

    if Location.all.empty?
      l1 = Location.create(name: "Test Location 1", code: 0001)
      l2 = Location.create(name: "Test Location 2", code: 0002)
    end

看起来好像没啥问题，用起来也没啥问题。但是如果把 `.sqlite3` 删掉再重新 `rake db:migrate` 的话（其实就是一个空项目直接迁移数据库的时候），就会报错，因为这会儿还没有 `Location` 呢。

然后好奇了一下 Rails 的启动顺序，就去翻了 Rails Guides 中关于 `initialization` 的[部分](http://guides.rubyonrails.org/initialization.html)。

这部分略(kan)复(bu)杂(dong)，~~但是可以增加逼格~~而且对我当前的任务来说也没啥用。我简(qi)单(shi)总(ye)结(bu)一(tai)下(dong)，在 guide 右边可以看到，启动顺序基本上就是按照 `railties/bin/rails`, `railties/lib/rails/app_rails_loader.rb` 等一步一步来的，然后到了 Loading Rails 这部分，就是 Rails 一些乱七八糟东西的加载了。

比如说从 `railties/lib/rails/all.rb` 里面可以看到 rails 首先把 ActiveRecord 等组件加载好。

然后 `Foo::Application.initialize!` 会调用 `railties/lib/rails/application.rb` 里面是这么写的：

    def initialize!(group=:default) #:nodoc:
      raise "Application has been already initialized." if @initialized
      run_initializers(group, self)
      @initialized = true
      self
    end

直到这个时候 `config/initializers/` 里面的内容才被加载上。

~~但是其实没解决什么问题…（趴~~

最终的解决方案是加入 `ActiveRecord::Base.connection.table_exists?('locations')`。

## create & create!

很奇怪，这个弱智的问题居然以前从来没有碰到过。

如果 `Location` 有这样的 validation:

    validates :name, presence: true

然后执行 `a = Location.new` 和 `b = Location.create` 其实是一样的，`create` 方法没有创建，也没有报错。官方文档是这么写的：

> The new method will return a new object while create will return the object and save it to the database.

这个让我在写测试的时候碰到了一些坑，可能漏掉了一个必填参数，但是没有报错。

~~个人总结：`Foo.create!` 比 `Foo.create` 好，因为 bang method 可以有效地报错。~~

2014.4.29 更新：

`create!` 会抛出异常而 `create` 不会。我的本意是想通过 `user = User.create(params)` 然后 `do_something if user`，然后发现使用 `if user.valid?` 即可。

## permit & strong parameters

刚转移到 Rails 4，发现没有了 `attr_accessible :name` 这样的东西。然后翻到了这篇关于 [Strong Parameters](http://weblog.rubyonrails.org/2012/3/21/strong-parameters/) 的 blog. 大意如下（又要瞎总结了）：

- `permit` 就是把 mass assignment 的控制从 model 转移到了 controller
- controller 其实就是一个通行权的控制，控制用户和 app 之间的那些东西，比如说鉴定、授权。所以就会有很多的参数传进来，所以 controller 控制这些东西也是理所当然的实情。
- 很早以前就有人使用 [slice pattern](https://gist.github.com/dhh/1975644) 来解决这样的问题了

## RecordNotSaved

这坑略蛋疼，大概就是说，假如说有这样的代码：

    class MyModel < ActiveRecord::Base
      before_save :assign_default_foo

    protected
      def assign_default_foo
        self.foo = false
      end
    end

这里使用了 `before_save` 来调用一个叫 `assign_default_foo` 的私有方法。然后调用了 `self.foo = false` 这条语句。

Ruby 的方法可以返回最后一条语句的结果，所以我们就不需要写 `return` 了，这个特性很棒，但是在这里就坑了别人。它会在堆栈中留下一个 `false`，然后 model 就不会被保存了…解决方案是在最后加上一行 `nil`，就像下面这样：

    def assign_default_foo
      self.foo = false
      nil
    end

参考：http://apidock.com/rails/ActiveRecord/RecordNotSaved

## undefined method `map' for "translation missing: zh-cn.date.order":String

这是 i18n 的问题，解决方案是在 `zh-CN.yml` 中添加如下代码：

    date:
      order:
      - :year
      - :month
      - :day

参考：https://github.com/svenfuchs/rails-i18n/issues/254
