---
layout: post
title: "Rails Guides 阅读计划：ActiveRecord Basics"
description: ""
category: "note"
tags: ['Ruby on Rails']
---
{% include JB/setup %}

# Rails Guides 阅读计划：ActiveRecord Basics

作者：[TheLover_Z](http://theloverz.me)

## Active Record

Active Record 是 Martin Fowler 的著作 [Patterns of Enterprise Application Architecture](http://book.douban.com/subject/1229954/) 中提出的。

在 Active Record 中，对象同事持有持久的数据（persistent data）和操作数据的行为（behavior which operates on that data）。

## optimistic locking

在 Active Record 中，有一些 optional column，比较常见的比如说 `created_at` 和 `updated_at`，还有一些别的。

比如说 `lock_version` 字段。它可以为 Active Record 加入 optimistic locking（好像中文翻译是「乐观锁机制」）。

通过 [Rails API](http://api.rubyonrails.org/classes/ActiveRecord/Locking/Optimistic.html) 可以知道，optimistic locking 允许多人同时编辑同一个记录，并且假定最小冲突。

原理是每次对数据的更新（update）都会增加这个 `lock_vertion`，然后如果有冲突，就会抛出 `StaleObjectError` 异常，API 里的例子很清楚，这里直接复制过来：

    p1 = Person.find(1)
    p2 = Person.find(1)

    p1.first_name = "Michael"
    p1.save

    p2.first_name = "should fail"
    p2.save # Raises a ActiveRecord::StaleObjectError

## single table inheritance

通过 `type` 关键字，可以触发单表继承。比如说：

    class Company < ActiveRecord::Base; end
    class Firm < Company; end
    class Client < Company; end
    class PriorityClient < Client; end

然后 `Company.where(name: '37signals').first` 会返回一个 `Firm` 对象。

## polymorphic associations

通过 `(association_name)_type` 可以指定多态。在 [Associations](http://guides.rubyonrails.org/association_basics.html#polymorphic-associations) 中有提到。大意是说，一个 model 可以 belong to 多于一个的 model. Guides 中的例子：

    class Picture < ActiveRecord::Base
      belongs_to :imageable, polymorphic: true
    end
     
    class Employee < ActiveRecord::Base
      has_many :pictures, as: :imageable
    end
     
    class Product < ActiveRecord::Base
      has_many :pictures, as: :imageable
    end

在这里，Picture 既 `belongs_to` Employee 又 `belongs_to` Product. 然后就可以同时使用 `@employee.pictures` 和 `@product.pictures` 这样的语句。

## update

`Foo.update` 的别名是 `update_attributes`.

而且还可以这么玩儿：

    User.update_all "max_login_attempts = 3, must_change_password = 'true'"

