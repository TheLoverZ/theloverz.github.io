---
layout: post
title: "Rails Guides - Rails Routing from the Outside In"
description: ""
category: "note"
tags: ['Ruby on Rails']
---
{% include JB/setup %}

# Rails Routing from the Outside In 笔记

## Singular Resources

使用 `resource :geocoder` 会生成除了 `index` 以外的 routes.

## namespace

使用

    namespace :admin do
      resources :articles, :comments
    end

可以生成类似于 `/admin/articles` 这样的 path.

如果不想使用 `/admin/` 这样的前缀的话，可以使用 `module`

    scope module: 'admin' do
      resources :articles, :comments
    end

类似的，如果不想要 `Admin::` Module 但是想要 `/admin/` 前缀，可以这么写

    scope '/admin' do
      resources :articles, :comments
    end

## Nested Resources

使用

    resources :magazines do
      resources :ads
    end

可以生成嵌套路由，比如说 `/magazines/:magazine_id/ads/:id`，但是 **永远不要** 嵌套多于一层。

## Route Globbing and Wildcard Segments

使用

    get 'photos/*other', to: 'photos#unknown'

可以匹配 `photos/12` 或者 `/photos/long/path/to/12`，只不过 `params[:other]` 可能会是 `12` 或者 `long/path/to/12`.

这种带星号的写法就叫做 wildcard segments.

而拿 `get 'books/*section/:title', to: 'books#show'` 去匹配 `books/some/section/last-words-a-memoir` 的结果是 `params[:section]` 等于 `'some/section'`，`params[:title]` 的值是 `'last-words-a-memoir'`.
