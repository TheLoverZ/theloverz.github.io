---
layout: post
title: "Rails Guides 阅读计划：ActiveRecord Migrations"
description: ""
category: "note"
tags: ['Ruby on Rails']
---
{% include JB/setup %}

# Rails Guides 阅读计划：ActiveRecord Migrations

作者：[TheLover_Z](http://theloverz.me)

## references

    rails generate migration AddUserRefToProducts user:references

会生成

    add_reference :products, :user, index: true

搜了一下好像是 Rails 4 [新出的功能](http://www.drurly.com/blog/2013/05/12/rails-add-references-migration)。

## JoinTable

Join 基于一个共同的字段，从两个或者更多的表中把多行进行结合。参考：[W3School](http://www.w3schools.com/sql/sql_join.asp).

Rails 中创建 join table 的方法：

    rails g migration CreateJoinTableCustomerProduct customer product

## seeds

想到之前生成初始化数据傻乎乎地在 `initializers` 里面初始化数据，其实应该在 `db/seeds.rb` 里面初始化。

## Type Modifiers

- `limit`: string/text/binary/integer 字段的最大规模。
- `precision`: `decimal` 字段的精度。
- `scale`: `decimal` 字段的 scale，即小数点后的位数。
- `polymorphic`: 给 `belongs_to` 加入 `type` 字段。这个在之前的 [ActiveRecord Basics](../rails-guides-activerecord-migrations/) 里面有说到。
- `null`: 是否允许 `NULL` 值。

直接照搬 Guides 里面的例子了。

    rails generate migration AddDetailsToProducts 'price:decimal{5,2}' supplier:references{polymorphic}

会生成：

    classAddDetailsToProducts < ActiveRecord::Migration
      defchange
        add_column :products, :price, :decimal, precision: 5, scale: 2
        add_reference :products, :supplier, polymorphic: true, index: true
      end
    end

