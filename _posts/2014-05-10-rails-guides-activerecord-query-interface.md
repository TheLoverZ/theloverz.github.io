---
layout: post
title: "Rails Guides 阅读计划：ActiveRecord Query Interface"
description: ""
category: "note"
tags: ['Ruby on Rails']
---
{% include JB/setup %}

# Rails Guides 阅读计划：ActiveRecord Query Interface

作者：[TheLover_Z](http://theloverz.me)

## each 和 find_each

数量很多的时候不建议用 `User.all.each`，因为这样一次性加载进了内存，建议用 `find_each`，它取回一块（batch）数据，然后每次 `yield` 一个。

或者也可以使用 `find_in_batches`，它取回一块数据然后整体作为一个 block 来 `yield`.

用法：

    User.find_each do |user|
      NewsLetter.weekly_deliver(user)
    end

    Invoice.find_in_batches(include: :invoice_lines) do |invoices|
      export.add_invoices(invoices)
    end

## Null Relation

    @posts = current_user.visible_posts.where(name: params[:name])
     
    def visible_posts
      case role
      when 'Country Manager'
        Post.where(country: country)
      when 'Reviewer'
        Post.published
      when 'Bad User'
        Post.none # => returning [] or nil breaks the caller code in this case
      end
    end

这样的代码，在 `Post.none` 一行，如果返回 `[]` 或者 `nil` 都会抛出异常。

    2.0.0-p451 :001 > Post.none
     => #<ActiveRecord::Relation []>

可以看到 `Post.none` 仍然是个 `Relation`，只不过是空的。

## optimistic/pessimistic locking

见之前的 [post](/note/2014/04/29/rails-guides-activerecord-basics/).

## n+1 queries

先看如下代码：

    clients = Client.limit(10)
     
    clients.each do |client|
      puts client.address.postcode
    end

这时候就会有 n+1 的问题，第一行执行了一次，找到 10 个 client，然后 `each` 循环中执行了十次。这样效率是很差的，可以这样来解决：

    clients = Client.includes(:address).limit(10)
     
    clients.each do |client|
      puts client.address.postcode
    end

通过 SQL 可以看到这样改写只执行了两次 SQL 查询：

    SELECT * FROM clients LIMIT 10
    SELECT addresses.* FROM addresses
      WHERE (addresses.client_id IN (1,2,3,4,5,6,7,8,9,10))

## find_or_xxx_by

`find_or_create_by` 和 `find_or_initialize_by` 顾名思义，找到的话就如同 `find` 操作，找不到的话就相当于 `create` 和 `new`.
