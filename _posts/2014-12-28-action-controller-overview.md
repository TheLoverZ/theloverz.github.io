---
layout: post
title: "Rails Guides - Action Controller Overview"
description: ""
category: "note"
tags: ['Ruby on Rails']
---
{% include JB/setup %}

# Action Controller Overview 笔记

## Routing Parameters

params hash 会包含 `:controller` 和 `:action` 两个参数，但是建议使用 `controller_name` 和 `action_name`.

## Strong Parameters

strong parameters 的目的是对 mass assignments 行为进行保护。所以你必须自己选择需要传递的参数。用法如下：

    params.require(:person).permit(:name, :age)

也可以使用 `permit!` 来白名单所有的参数：

    params.require(:log_entry).permit!

当然嵌套的 attributes 也可以 `permit`：

    params.permit(:name, { emails: [] },
                  friends: [ :name,
                             { family: [ :name ], hobbies: [] }])

如果使用了 `accepts_nested_attributes_for`，不要忘了 `permit` `:id` 和 `:_destroy`.

## filters

`before_action` 等都可以使用 block：

    before_action do |controller|
      unless controller.send(:logged_in?)
        flash[:error] = "You must be logged in to access this section"
        redirect_to new_login_url
      end
    end

## Request Forgery Protection

一般来说 `form_for` 或 `form_tag` 都包含 token，但你可以使用 `form_authenticity_token` 来手动取得 token.
