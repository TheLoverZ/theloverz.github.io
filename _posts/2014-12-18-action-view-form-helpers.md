---
layout: post
title: "Rails Guides - Action View Form Helpers"
description: ""
category: "note"
tags: ['Ruby on Rails']
---
{% include JB/setup %}

# Action View Form Helpers 笔记

## search forms

永远使用 GET 方法，因为这样可以加浏览器书签。

## PATCH/PUT/DELETE

Rails 鼓励使用 RESTful 设计，所以会用到 `PATCH`, `PUT`, `DELETE`. 但是有些浏览器并不支持这些方法。所以 Rails 会使用一个叫 `_method` 的 hidden input 来模拟。

    <form accept-charset="UTF-8" action="/search" method="post">
      <input name="_method" type="hidden" value="patch" />
      <input name="utf8" type="hidden" value="&#x2713;" />
      <input name="authenticity_token" type="hidden" value="f755bb0ed134b76c432144748a6d4b7a7ddf2b71" />
      ...
    </form>

## Select Boxes

Guide 先用了这样一个例子：

    <select name="city_id" id="city_id">
      <option value="1">Lisbon</option>
      <option value="2">Madrid</option>
      ...
      <option value="12">Berlin</option>
    </select>

这是最基本的写法，但是很麻烦。其中 `option` 部分可以使用 `options_for_select` 来简化：

    <%= options_for_select([['Lisbon', 1], ['Madrid', 2], ...]) %>

再加上 `select_tag`：

    <%= select_tag(:city_id, '<option value="1">Lisbon</option>...') %>

组合一下可以这么写：

    <%= select_tag(:city_id, options_for_select(...)) %>

需要注意的是 `options_for_select` 第二个参数必须完全匹配你想要的值。比如说 `"2"` 和 `2` 就是不同的两个值。

## 更复杂的 form

构建嵌套 attr 最主要就是使用 `accepts_nested_attributes_for` 来创建：

    class Person < ActiveRecord::Base
      has_many :addresses
      accepts_nested_attributes_for :addresses
    end

    class Address < ActiveRecord::Base
      belongs_to :person
    end

这样会在 `Person` 上生成一个 `addresses_attributes=` 方法来允许你对 address 进行创建或更改。

然后使用如下 view 代码：

    <%= form_for @person do |f| %>
      Addresses:
      <ul>
        <%= f.fields_for :addresses do |addresses_form| %>
          <li>
            <%= addresses_form.label :kind %>
            <%= addresses_form.text_field :kind %>

            <%= addresses_form.label :street %>
            <%= addresses_form.text_field :street %>
            ...
          </li>
        <% end %>
      </ul>
    <% end %>

使用 `allow_destroy: true` 参数可以使得自动删除关联的对象。
