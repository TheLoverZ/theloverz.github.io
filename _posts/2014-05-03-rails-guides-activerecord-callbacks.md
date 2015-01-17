---
layout: post
title: "Rails Guides 阅读计划：ActiveRecord Callbacks"
description: ""
category: "note"
tags: ['Ruby on Rails']
---
{% include JB/setup %}

# Rails Guides 阅读计划：ActiveRecord Callbacks

作者：[TheLover_Z](http://theloverz.me)

## callbacks 被调用顺序

创建一个对象的时候：

- before_validation
- after_validation
- before_save
- around_save
- before_create
- around_create
- after_create
- after_save

更新一个对象的时候：

- before_validation
- after_validation
- before_save
- around_save
- before_update
- around_update
- after_update
- after_save

销毁一个对象的时候：

- before_destroy
- around_destroy
- after_destroy

## after_initialize 和 after_find

`after_initialize` 在一个 ActiveRecord 对象被创建的时候会被调用，从数据库取出一条记录的时候也会被调用。

`after_find` 在从数据库取出一条记录的时候被调用，是 `after_initialize` 的后半部分。

如果同时被定义，那么 `after_find` 先被调用。

## callback chain

每注册的一个 callback 都会被加到 callback chain 中，如果有 `before` callback 返回了 `false` 或者抛出了异常，那么整个 chain 就会 halt，然后回滚。而 `after` callback 只会在抛出异常的时候回滚。

任何不是 `ActiveRecord::Rollback` 的异常会在 halt 以后重新被抛出。

## relational callbacks

这个之前说过，就比如说一个 User `has_many` posts，然后 association 就可以这么写：

    has_many :posts, dependent: :destroy

然后删掉 user 的同时，与之关联的 posts 也被删掉了。

## after_commit 和 after_rollback

这两个 callback 是用于数据库的。前者发生在数据库 transaction 被提交以后，后者发生在数据库回滚以后。

## 其他小 tips

安全起见，callback methods 应该是 `protected` 或者 `private`.
