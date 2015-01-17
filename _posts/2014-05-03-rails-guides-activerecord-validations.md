---
layout: post
title: "Rails Guides 阅读计划：ActiveRecord Validations"
description: ""
category: "note"
tags: ['Ruby on Rails']
---
{% include JB/setup %}

# Rails Guides 阅读计划：ActiveRecord Validations

作者：[TheLover_Z](http://theloverz.me)

## new_record?

`new_record?` 可以用来判断一个对象是否准备存入数据库。直接抄 Guides 的代码：

    $ rails console
    >> p = Person.new(name: "John Doe")
    => #<Person id: nil, name: "John Doe", created_at: nil, updated_at: nil>
    >> p.new_record?
    => true
    >> p.save
    => true
    >> p.new_record?
    => false

## acceptance

可以用于 checkbox 的验证，而且这是个虚拟属性，不需要在数据库中存在。

用法：

    validates :terms_of_service, acceptance: true

## validates_associated

之前老会写这样的 rspec 测试代码：

    User.associations.should eq [:products, :foo, :bar]

其实使用 `validates_associated :products` 这样的 validation 就可以了。

哦对了，很重要的一件事，不能在 associations 的两边都使用 `validates_associated`，否则死循环什么你懂的。

## confirmation

这个和 `acceptance` 有点儿类似，也是一个虚拟属性，用于注册表单中的「Email 地址」「确认 Email 地址」这样的东西。

在 Model 中这么写：

    class Person < ActiveRecord::Base
      validates :email, confirmation: true
    end

在 View 中这么写：

    <%= text_field :person, :email %>
    <%= text_field :person, :email_confirmation %>

哦对了，也很重要的一件事……（你够

`confirmation` 只会在非 `nil` 的时候验证。所以需要这么写：

    validates :email, confirmation: true
    validates :email_confirmation, presence: true

（个人觉得这样挺蛋疼的……

## length validations

对 length 进行验证的时候可以用 `:too_long` 和 `:too_short` 自定义错误信息。

## numericality validations

这个可以定制的就多了…`:greater_than`, `:greater_than_or_equal_to`, `:equal_to`, `:less_than`, `:less_than_or_equal_to`, `:odd`, `:even`. 基本上都是顾名思义。

## presence 和 absence

`presence` 的定义是："This helper validates that the specified attributes are not empty."

`absence` 的定义是："This helper validates that the specified attributes are absent."

再看 default error message，分别是：

`presence`:

> The default error message is "can't be blank".

`absence`:

> The default error message is "must be blank".

所以 `presence` 和 `absence` 是相反的关系。

## uniqueness

确保（姑且这么说）某个键是唯一的。用法：

    validates :email, uniqueness: true

上面我说了「姑且」，其实如果两个不同的数据库连接创建相同的一个记录，那么这个 validation 是没有办法的。

## conditional validation

可以使用 `if` 或者 `unless` 来限制 validations 发生的时间。用法一目了然，直接 copy 过来：

    class Order < ActiveRecord::Base
      validates :card_number, presence: true, if: :paid_with_card?
     
      def paid_with_card?
        payment_type == "card"
      end
    end

## 其他小 tips

创建/更新失败的时候。`create` 会返回对象（即 `new` 创建的），而 `update` 和 `save` 会返回 `false`.

`errors.messages` 可以查看 validation 抛出的错误。

`false.blank?` 的结果是 `true`. 所以验证 boolean 的时候需要这么写：`inclusion: { in: [true, false] }`.