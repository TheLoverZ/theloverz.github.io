---
layout: post
title: "Rails Guides - Layouts and Rendering in Rails"
description: ""
category: "note"
tags: ['Ruby on Rails']
---
{% include JB/setup %}

# Layouts and Rendering in Rails 笔记

## responses

从 controller 的角度来看，有三种方法来创建一个 http response：

- 使用 `render` 来生成一份完整的 response
- 使用 `redirect_to` 来传递 HTTP 重定向状态码（HTTP redirect status code）
- 使用 `head` 来传递 HTTP headers 信息

## content_type

`content_type` 用于指定 MIME，默认的是 `text/html` 或者 `application/json` 等。

    render file: filename, content_type: "application/rss"

## 在 runtime 指定 layout

使用 Symbol 来在 runtime 指定 layout

    class ProductsController < ApplicationController
      layout :products_layout

      def show
        @product = Product.find(params[:id])
      end

      private
        def products_layout
          @current_user.special? ? "special" : "products"
        end

    end

## Double Render Errors

基本上所有的 Rails Developers 都见过类似于 `Can only render or redirect once per action` 的问题。这是因为对 `render` 的理解不太正常。看下面的代码：

    def show
      @book = Book.find(params[:id])
      if @book.special?
        render action: "special_show"
      end
      render action: "regular_show"
    end

如果 `@book.special?` 那么就会 `render` 到 `special_show`，然后这里 **并不会** 停止，而是 **继续执行**。这就是问题所在。最简单的解决方案就是加上 `and return` 确保这里跳出了，即：

    render action: "special_show" and return
