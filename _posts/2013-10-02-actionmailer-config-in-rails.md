---
layout: post
title: "ActionMailer config in Rails"
description: ""
category: "note"
tags: ['Ruby on Rails']
---
{% include JB/setup %}

# Rails ActionMailer 配置

作者：[TheLover_Z](http://theloverz.me)

别的安装过程在网上都可以找到，只是具体的配置真心折腾好久……

以下配置在 Ruby 1.9.3，Rails 3.2.13 可用。

## Gmail 配置

配置均指 `config/application.rb`，下同。

    ActionMailer::Base.smtp_settings = {
      :address              => "smtp.gmail.com",
      :port                 => 587,
      :domain               => "gmail.com",
      :user_name            => username,
      :password             => password,
      :authentication       => "plain",
      :enable_starttls_auto => true
    }

## QQ 企业邮箱配置

    ActionMailer::Base.smtp_settings = {
      :address              => "smtp.exmail.qq.com",
      :port                 => 25,
      :domain               => "mail.qq.com",
      :user_name            => username,
      :password             => password,
      :authentication       => :login,
    }
