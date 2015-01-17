---
layout: post
title: "From Sublime to Emacs"
description: ""
category: 'tutorials'
tags: ['emacs', 'sublime']
---
{% include JB/setup %}

# 从 Sublime 到 Emacs（不断更新中）

作者：[TheLover_Z](http://theloverz.me)

## 初始化配置

安装下载 Emacs 24，强烈建议按 `C-h t` 来把教程走一遍，然后基本上常用的快捷键已经掌握得差不多了。

比如说这几个我觉得很常用的：

- 对于新手来说最重要的一个，`C-g`，觉得有什么不对劲或者按错了什么都可以用 `C-g` 来结束掉。
- `C-n` 下移一行，`C-p` 上移一行，其实不常用，更常用的是 `M-g g` 然后输入行号来跳转。
- `C-v` 向下翻页，`M-v` 向上翻页。
- `C-l` 把当前行显示在屏幕中央，再按一次就显示在屏幕顶部。
- `C-s` 正向搜索，多次匹配的话再按几次就好了。`C-r` 逆向匹配。
- `C-x C-s` 保存。
- `C-a` 行首，`C-e` 行尾。
- `C-/` 撤销。
- `C-x b` 切换 buffer，`C-x C-b` 列出所有 buffer.
- `C-x 2` 多 buffer（2 也可以是别的数字），`C-x 1` 单 buffer.

上面如果哪个你觉得不熟悉的话建议多练习几遍。

如果想要查帮助就用 `C-h k key`，比如说你想查 `C-x b` 就可以按 `C-h k C-x b` 来看帮助。

## 新手包？

网上大部分教程都说要使用新手包，对于新手来说需要模仿高手的配置，但是我想在这里说点儿不同的观点。

正如大多数人一样，我一开始安装了 starter kit，觉得好像没什么变化，然后我又安装了 purcell 的配置，还是没什么变化……

我觉得问题在于，我安装了新手包，但我不知道到底增加了什么新东西，比如说我一直在找 Sublime 的 goto anything 功能，却不知道新手包有没有包含进来，也不知道怎么使用。

所以我的建议是，尽量自己配置，这样你可以知道新增加了什么东西。我也还是个新手，所以希望高手能谈谈自己的看法。

## 安装新手包

上面说了那么多，但是其实新手包也没什么坏处不是…我个人觉得 starter kit 比 purcell 的好用一些。据说 purcell 的新手包不太稳定。

先把 starter-kit clone 下来：`git clone http://github.com/eschulte/emacs24-starter-kit.git`

然后移动到 `~/.emacs.d` 就好。这时候启动 Emacs 就会看到一大串的安装，不用管它。

## 常见问题

这里有几个一开始我也掉坑里的地方，放在这里供和我一样的新手参考。

buffer 可以理解成打开的多个文件。frame 就是多窗口。

配置文件指的是 `~/.emacs`（没有就新建），或者 `~/.emacs.d/init.el`，个人建议新手放在前面那里，有问题也好修改。因为 `init.el` 一般都有新手包默认的设置，新手容易弄乱。

`.emacs` 怎么写？这里有几个常用的配置，以供新手参考：

    ;;load-path 必须的
    (setq load-path (cons (expand-file-name "~/.emacs.d") load-path))

    ; 这几个都是包管理，也是必须的
    (require 'package)
    (add-to-list 'package-archives
      '("elpa" . "http://tromey.com/elpa/"))
    (add-to-list 'package-archives
      '("marmalade" . "http://marmalade-repo.org/packages/"))

    ;;高亮显示要拷贝的区域
    (transient-mark-mode t)

    ;;显示匹配的括号
    (show-paren-mode t)

    ;;用 y/n 替代 yes/no
    (fset 'yes-or-no-p 'y-or-n-p)

    ;行号
    (global-linum-mode 1)

包管理按 `M-x package-list-packages`，然后找你需要的包就行了。

在终端输入 `emacs` 打开的是终端里面的 emacs，而且版本号也不是 24 怎么办？

对于 Mac OS X 来说，你需要把如下这个脚本放在 `/usr/bin/emacs`，之前的 emacs 可以移走也可以删掉，脚本如下：

    #!/bin/sh
    /Applications/Emacs.app/Contents/MacOS/Emacs "$@"

最后，不要忘了 `chmod +x /usr/bin/emacs`.

## 块操作

块操作就相当于是鼠标拖拉一大块内容然后进行操作。

使用 C-spc（ctrl/command+space）设定起点，对于热键冲突的情况可以用 `Ctrl+shift+2` 也就是 `C-@`，然后就可以选择终点了（比如说 C-r 或者 C-p/n 等等）。

常用操作有：

- `M-w` 复制
- `C-y` 粘贴
- `C-u Tab` 用于多行缩进
- `M-x comment-region` 多行注释，`M-x uncomment-region` 取消注释

什么？你嫌最后一行两个命令太长？可以用 `tab` 补全的啊同学……

## goto anything

这个是折腾得最头疼的一步，最终找到了 `projectile` 这个插件。正如这个插件的名字显示的那样，你需要在一个 project 下面进行查找，直接在终端 `emacs path/to/project` 就好了。

安装以后使用 `M-x projectile-global-mode` 打开 projectile mode.

然后有如下快捷键：

- `C-c p f` 查找文件，相当于 Sublime 的 command+p.
- `C-c p g` 查找内容，相当于 Sublime 的 shift+command+f.

## markdown 模式

这篇文章就是用 Emacs 写的，使用方法如下：

- `M-x package-list-packages`.
- 找到 `markdown-mode` 并且下载（这里有个小技巧，`C-x o` 可以切换活跃 buffer，然后 `tab` 键就可以跳到 Install 字样，`y`，回车）。
- 把 `markdown-mode.el` （我这里是在 `~/.emacs.d/elpa/markdown-mode-1.9/`）拷贝到 `~/.emacs.d` 下面。
- 然后 `M-x markdown-mode` 就可以了。

如果你想要自动检测 `.md` 文件来进行自动切换 mode 的话可以使用这个配置：

    (autoload 'markdown-mode "markdown-mode"
      "Major mode for editing Markdown files" t)
    (add-to-list 'auto-mode-alist '("\\.markdown\\'". markdown-mode))
    (add-to-list 'auto-mode-alist '("\\.md\\'". markdown-mode))

## 主题

个人一直很喜欢 Sublime 的默认主题，这样安装：

`M-x package-list-packages` 然后找到 `monokai-theme`，安装以后像上面说的那样把 `.el` 拷到 `~/.emacs.d`.

然后在 `.emacs` 加入这么一句：`(require 'monokai-theme)`，重启就好了。

## 修改配置以后不重启 Emacs 生效

  M-x load-file ~/.emacs

## 左右分屏

  C-x 3

## Ruby on Rails

安装 `ruby-eletric` 然后你就有了自动补全。

## 更多设置

  ;去掉工具栏
  (tool-bar-mode nil)

  ;不要滚动条
  (scroll-bar-mode nil)

## fix: `symbol's value as variable is void: last-command-char`

这是因为 ruby-mode 使用了已经废弃的 `last-command-char` 常量，网上有很
多解决方案，我试了试感觉还是如下解决方案最好：

把上文 `~/.emacs` 中包管理的 6 行全部删掉，改为下面的：

    (setq package-archives '(("gnu" . "http://elpa.gnu.org/packages/")
        ("marmalade" . "http://marmalade-repo.org/packages/")
        ("melpa" . "http://melpa.milkbox.net/packages/")))

对，`(require 'package)` 那句也不要了。

然后把 `~/.emacs.d/elpa` 下面的 `ruby-electric` 删掉，再使用 `M-x
package-list-packages` 安装一遍就好了。

## 一些有用的插件

### web-mode

我主要把这个用来处理 Ruby on Rails 的 `.erb` 文件。可以自动补全 `<%= %>` 等。但是使用过程中发现高亮其实还是有问题的。

在包管理找到 `web-mode` 安装，然后把 `web-mode.el` 移到 `load-path` 下，并且在 `.emacs` 加入如下配置：

    (require 'web-mode)
    (add-to-list 'auto-mode-alist '("\\.phtml\\'" . web-mode))
    (add-to-list 'auto-mode-alist '("\\.tpl\\.php\\'" . web-mode))
    (add-to-list 'auto-mode-alist '("\\.jsp\\'" . web-mode))
    (add-to-list 'auto-mode-alist '("\\.as[cp]x\\'" . web-mode))
    (add-to-list 'auto-mode-alist '("\\.erb\\'" . web-mode))
    (add-to-list 'auto-mode-alist '("\\.mustache\\'" . web-mode))
    (add-to-list 'auto-mode-alist '("\\.djhtml\\'" . web-mode))

### grizzl

其实这个东西我个人觉得就是把 projectile 变得更好看了一点，而且键位也更好用了（虽然可以直接自定义）。

安装 `grizzl` 以后配置如下：

    (require 'grizzl)
    (setq projectile-enable-caching t)
    (setq projectile-completion-system 'grizzl)
    (global-set-key (kbd "s-p") 'projectile-find-file)
    (global-set-key (kbd "s-b") 'projectile-switch-to-buffer)

然后就可以用 `cmd + p` 来替代之前的 `C-c c p` 了…但是 `cmd + b` 我这里绑定失败……

### hightlight-indentation

看名字应该知道这个就是高亮缩进，配置如下：

    (require 'highlight-indentation)
    (add-hook 'enh-ruby-mode-hook
        (lambda () (highlight-indentation-current-column-mode)))
    (add-hook 'coffee-mode-hook
        (lambda () (highlight-indentation-current-column-mode)))
    (set-face-background 'highlight-indentation-face "#343557")
    (set-face-background 'highlight-indentation-current-column-face "#345c9c")

然后可以使用 `highlight-indentation-current-column-mode` 和 `highlight-indentation-mode` 来开启缩进提示。问题是前者可能不太准（Ruby on Rails 环境），后者的线条实在是太粗了……

### robe

这个是我最近用的最满意的一个插件了，直接放文档：

- M-. to jump to the definition
- M-, to jump back
- C-c C-d to see the documentation
- C-c C-k to refresh Rails environment
- C-M-i to complete the symbol at point

大概说说，就是 `M-.` 可以跳转到方法定义的地方，看过以后再用 `M-,` 跳回来。但是呢，后来我发现 `C-c C-d` 更好用一点因为它自动会再给你开一个窗口提示这个方法的定义是什么样的。

在包管理搜索 `robe-mode` 然后配置：

    (add-hook 'ruby-mode-hook 'robe-mode)

如果遇到 `Your Ruby version is 1.8.7, but your Gemfile specified 2.0.0` 类似的提示，用包管理安装 `rvm`，然后在 `.emacs` 添加：

    (require 'rvm)
    (rvm-use-default)
