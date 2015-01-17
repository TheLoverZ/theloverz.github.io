---
layout: post
title: "Chrome Extensions Notes"
description: ""
category: "note"
tags: ["chrome extension", "javascript"]
---
{% include JB/setup %}

# Chrome Extension 开发笔记

作者：[TheLover_Z](http://theloverz.me)

为了治好强迫症，写了个[插件](http://www.v2ex.com/t/106957)，期间碰到无数坑，记录一下。

## jQuery 等外部 JS 库

在 `manifest.json` 中加入类似如下代码：

    "content_scripts":[{
      "js":["javascript/jquery-2.1.0.min.js"]
    }],

或者

    "background":{
      "scripts": ["javascript/jquery-2.1.0.min.js"]
    }

而且不可以使用内联。

## 路径

感觉有点儿奇怪，比如说我的文件树如下：

    > tree .
    .
    ├── 1.png
    ├── 2.png
    ├── README.md
    ├── css
    │   └── bootstrap.min.css
    ├── icon.png
    ├── javascript
    │   ├── background.js
    │   ├── bootstrap.min.js
    │   ├── common.js
    │   └── jquery-2.1.0.min.js
    ├── manifest.json
    ├── new\ in\ v2ex.crx
    └── views
        └── new.html

    3 directories, 12 files

但是我在 `manifest.json` 必须使用 `javascript/jquery-2.1.0.min.js` 而不是更简单的 `jquery-2.1.0.min.js`.

## permission

仍然是 `manifest.json`，其中的 permissions 部分应该是：

    "permissions":
      [
        "http://api.flickr.com/",
        "http://www.baidu.com/",
        "http://www.v2ex.com/"
      ],

结尾的 `/` 不能少。
