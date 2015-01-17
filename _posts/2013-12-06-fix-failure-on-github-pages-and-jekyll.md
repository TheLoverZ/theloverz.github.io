---
layout: post
title: "Fix failure on Github Pages and Jekyll."
description: ""
category: "note"
tags: ['Github', 'Jekyll']
---
{% include JB/setup %}

# 修复 Github Pages 出现的 Page build failure

作者：[TheLover_Z](http://theloverz.me)

## 症状

之前一直好好的，昨天晚上突然发现疯狂报错。症状是 `git push origin master` 以后收到来自 Github 的信：

    The page build failed with the following error:

    The submodule `_theme_packages/the-minimum` was not properly initialized with a `.gitmodules` file.

    For information on troubleshooting Jekyll see:

      https://help.github.com/articles/using-jekyll-with-pages#troubleshooting

    If you have any questions please contact us at https://github.com/contact.

在网上搜了很多办法都不行，后来跟 Github 联系了以后发现是 Jekyll Bootstrap 没有正确安装（我也不知道…）

## 解决方案

删掉 `_site` 和 `_theme_packages` 这两个文件夹

在 `.gitignore` 加入这两行（没有则创建一个新的）：

    _site/*
    _theme_packages/*

再次按照 [Using Themes](http://jekyllbootstrap.com/usage/jekyll-theming.html) 这里重新安装主题…

然后就好了…
