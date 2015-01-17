---
layout: post
title: "git by tutorials"
description: ""
category: "tutorials"
tags: ['git']
---
{% include JB/setup %}

# Git by Tutorials

作者：[TheLover_Z](http://theloverz.me)

## 简介

目的是为了让您看完这个以后能学会 git 的基本操作。日常使用基本无压力。

因为我不打算讲任何迷惑新手的原理，只是教你会用。所以如果您是 git 高手，那您就直接关掉页面就好了。但是如果您几乎没用过 git 的话，那么这么教程就比较适合您。

下文的所有代码全在我本机测试过，希望您能跟着做一遍。

如果有问题或者任何意见建议，欢迎您[联系我](mailto:sethverlo5@gmail.com)。

## git 历史

参见[维基百科](http://zh.wikipedia.org/zh-cn/Git)。

## 安装 git

请自行 Google.

## 配置 git

安装好 git 以后，为了能让 git 认识你，请在终端输入下面的命令（不过请把 TheLover_Z 和 sethverlo5@gmail.com 替换为您自己的名字和邮箱），这个命令的目的是设置 git 基本信息。

    git config --global user.name "TheLover_Z"
    git config --global user.email sethverlo5@gmail.com

配置完成以后可以用 `git config --list` 来查看系统信息，比如说在我的机子上就可以看到这样的信息：

    user.name=TheLover_Z
    user.email=sethverlo5@gmail.com
    color.branch=auto
    color.diff=auto
    color.status=auto
    mergetool.keepbackup=true

不要纠结后面四行的信息，当作没看到。正如我之前说的，我不会在这里掺杂很多迷惑新手的东西，我也是刚从初学者一步一步走过来，我知道坑可能会在哪里，你可能会卡在哪里，所以我会尽量带您避开。如果您有兴趣深入了解 git 和原理相关，请多问问 Google.

## 初始化一个新的 git 仓库

初始化一个新的仓库意思就是，当前这个文件夹以后就归 git 管理了。文件夹还是原来的文件夹，只不过加入了一点点特殊的东西，不要担心，对您没有任何影响。

初始化的命令是：`git init`，然后会看到类似这样的提示：

    ➜ git init
    Initialized empty Git repository in /Users/thelover_z/Desktop/gitByTutorials/.git/
    ➜ git:(master) ✗

好了，现在就新建成功了，你看最后一行是 `git:(master)` 意思是现在在 master 分支下。master 就是最常用的分支。如果你不知道分支是什么意思，先别急，后面会讲到的。

## git 的基本操作

先随便编辑一个文件，但是为了能让您的结果和我的结果尽可能一致，这里我们新建一个 `a.txt` 文件，内容只有一个 a 字符。你可以用 vim 或者别的编辑器来完成。我直接给您看结果：

这是 `a.txt` 的内容：

![a.txt](/images/git_by_tutorials/a.png)

然后运行 `git status`，意思是查看目前的状态，您可以看到如下的结果：

    # On branch master
    #
    # Initial commit
    #
    # Untracked files:
    #   (use "git add <file>..." to include in what will be committed)
    #
    #	a.txt
    nothing added to commit but untracked files present (use "git add" to track)

意思是 `a.txt` 这个文件没有被追踪（Untracked files 字样）。如果你这个文件夹下有不能见人的小电影，那你肯定不想让别人看到，对吧？所以你就添加你觉得需要追踪的文件就可以了。

敲 `git add a.txt`，然后再看一下当前状态（想一下应该用什么命令？），可以看到类似于下面的结果：

    # On branch master
    #
    # Initial commit
    #
    # Changes to be committed:
    #   (use "git rm --cached <file>..." to unstage)
    #
    #	new file:   a.txt

意思是这个 `a.txt` 已经被添加到追踪了，但是还没有提交这个修改（to be committed）。我们来提交一下，输入 `git commit -m "first commit"`，最后的 first commit 你可以按你的想法来写，意思就是让你能想起这次修改了什么东西，比如说「提升了 100 倍性能！」这样的也可以。

现在你学会怎么追踪一个新文件和怎么提交修改了。

## .gitignore 文件

这个文件的意思就是 git ignore，就像前面说的，你这个文件夹下有不想让别人看到的东西，比如小电影啊密码啊什么的东西，然后你就可以编辑这个文件。

我们来试试，新建一个 `password.txt` 里面随便写点儿什么都行，假如说这个里面有关键性的密码。我们想要以后都忽略掉这个东西，怎么办？

很简单，新建一个`.gitignore` 文件（别忘了前面有一个点）。然后编辑，增加一行 `password.txt`，再追加一下这个文件（`git add` 命令），提交（`git commit` 命令），就 ok 了。完整步骤如下：

    vim password.txt

编辑好了 `password.txt` 文件，然后添加这个文件到 git 追踪系统中：

    git add .gitignore

然后用 `git status` 查看一下现在的状态：

    # On branch master
    # Changes to be committed:
    #   (use "git reset HEAD <file>..." to unstage)
    #
    #	new file:   .gitignore
    #

提交这次修改：

    git commit -m "add gitignore"

好了，现在你学会怎么用 `.gitignore` 来忽略对某些文件的追踪了。

如果您知道什么是通配符，那么还需要注意点，比如说你想忽略当前文件夹下所有的 `.txt` 文件，可以写 `*.txt`. 如果你不知道什么是通配符，请您当做没有看到这一段。

## 分支（branch）

想象一下你正在写一个软件，一般来说，这个软件的稳定版可能就是 master 分支。master 分支又叫做主分支。

然后你突然想到了一个激动人心的 idea! 你会怎么做？

直接在稳定版上面修改不太好吧？这个时候比较好的办法是再开一个分支，比如可以叫 `brilliant`，后续在 `brilliant` 下的工作都在这个分支下，而对主分支没有任何修改（我想再重复一遍，没有任何修改）。

然后你又突然发现稳定版有一个 bug，`brilliant` 先放一边，而着手修复这个 bug，你可以再开一个分支，比如说叫 `bug` 分支，弄好以后，把这个 `bug` 分支合并到主分支，然后你就可以继续做 `brilliant` 了，然后在合适的情况下合并到主分支。

好了，现在你知道分支是做什么的了。我们来试试 branch 的常用操作。

我们现在只有一个叫做 `master` 的分支，就像图 1 显示的那样。

![图 1](/images/git_by_tutorials/1.png)

图 1

现在我们来建立第一个 branch，叫做 `a` 好了。

    git branch a

这样你可以得到一个新的 branch 叫 `a`，这个 `a` 是从 `master` 衍生过来的。

然后使用 `git checkout a` 切换到这个 branch.

我们向 `a.txt` 增加一行，在第二行也输入一个 a，然后保存提交，请你自己根据上文来完成（提示：先 `git add` 然后 `git commit`）。现在的状态应该像图 2 所示。

![图 2](/images/git_by_tutorials/2.png)

图 2

再添加一个 `b` 分支（提示，想想现在在哪个分支？需不需要切换一下？切换命令是？如果不切换应该会有什么不同？），`a.txt` 不变，但是添加一个新文件 `b.txt` 内容只有一个 `b`，如下图所示：

![图 3](/images/git_by_tutorials/3.png)

图 3

现在你应该知道 branch 是什么东西了，而且也知道怎么创建新的 branch 了。不过既然有分支肯定就有合并，下面我们来说说分支的合并。

## 合并（merge）

现在我们来尝试一下合并操作（merge），想一下，如果把 `a` 分支合并到 `master` 会是什么样子？

使用 `git checkout master` 切换到 master，然后使用 `git merge a` 来进行 merge 操作。好，这段话请你再看一遍，分清主次顺序。

然后就像想象中的一样，`a.txt` 的内容现在是两行 a.

好了，`a` 这个 branch 现在没什么用了，删掉的命令是 `git branch -d a`.

现在的情况应该是图 4 显示的那样：

![图 4](/images/git_by_tutorials/4.png)

图 4

现在想一下如果把 b 也合并到 master 会是什么样子？等等，这两个 branch 的 `a.txt` 内容不一样，猜一下 git 会怎么处理？

git 会很聪明地帮你处理好几乎一切，试一下合并操作，现在 `a.txt` 里面仍然是两行 a. 而且 `b.txt` 也被添加到了 master 分支。

不知道你会不会已经猜到了，还有一种情况可能会出现冲突，我们来看一下冲突是怎么回事和怎么解决冲突。

先用 `git branch -d` 命令（哎你别忘了在 `-d` 后面加参数啊）把原先的除了 master 之外的 branch 都删掉。然后再次新建两个分支分别叫 a 和 b，然后按照下图进行创建和修改：

![图 5](/images/git_by_tutorials/5.png)

图 5

然后切换到 master 下面，先 `git merge a` 没有问题，再 `git merge b`，然后可以看到这样的提示：

    Auto-merging a.txt
    CONFLICT (content): Merge conflict in a.txt
    Automatic merge failed; fix conflicts and then commit the result.

这时候 git 就不知道该怎么办了，它会求救于你。这时候看一下 `git status` 可以看到这样的提示：

    # On branch master
    # You have unmerged paths.
    #   (fix conflicts and run "git commit")
    #
    # Unmerged paths:
    #   (use "git add <file>..." to mark resolution)
    #
    #	both modified:      a.txt
    #
    no changes added to commit (use "git add" and/or "git commit -a")

从逻辑上来说，这种冲突只能人为解决。运行 `git mergetool`，然后会有一个可视化的环境来帮助你完成冲突解决操作。

现在你应该知道怎么使用 branch 了。

## 远程操作

目前我们所有的操作都是在本地执行的，但是如果你听说过 git 那么你应该也听说过 github 或者 heroku 等服务。

如果你想参与到 github 的开源贡献中，应该怎么办呢？你可以使用 `git clone` 命令。比如说 [cow](https://github.com/cyfdecyf/cow) 这个开源项目，你可以看到有 `https://github.com/cyfdecyf/cow.git` 这样的 url. 克隆到本地就是 `git clone https://github.com/cyfdecyf/cow.git`.

然后你可以看到下面这样的结果：

    Cloning into 'cow'...
    remote: Counting objects: 2963, done.
    remote: Compressing objects: 100% (1246/1246), done.
    remote: Total 2963 (delta 1708), reused 2939 (delta 1688)
    Receiving objects: 100% (2963/2963), 962.53 KiB | 69 KiB/s, done.
    Resolving deltas: 100% (1708/1708), done.

如果这是 *你自己* 的项目（这里我说必须是「你自己」的项目是因为，你不可能随便对别人的源码进行修改，对吧？），你做完了想再次更新到 github，那么使用 `git push` 命令就可以了。

`git push` 默认是推送到 origin 仓库的 master 分支。等等，origin 又是什么东西？这个是仓库名，想象一下，你维护了多个开源代码，每一个代码库存放的地方可能都不太一样。这就会有 origin 等服务器的区别。

完整的 `git push` 命令应该是 `git push origin master`，当然，你已经知道了 origin 是可以被替换掉的，你也知道 master branch 是什么了。如果你想把 `a` branch 推过去，命令就应该是 `git push origin a`.

## git 有后悔药

既然是版本管理，那么就可以回溯和修正你之前犯的错误。

### 回溯到某个早期版本

这可能是比较常用的一个回溯方法了，比如你在修改文档，突然发现从某个地方开始你全部做错了，想回到那个时间点重新来。世上没有后悔药，可是你有 git.

练习时间到！新建一个文件夹，`git init`，然后编辑 `a.txt`，输入下面的内容：

    git is fun
    programming is fun
    computer is also fun

然后 `git add a.txt`, `git commit -m "first"`. 等等，没有我的提示你也知道该做什么的对吧？

然后把 `a.txt` 第二行删掉。再次 `git add` 和 `git commit`.

啊哦！然后你突然想起来第二次修改是不对的，怎么办？你需要先看一下提交历史，用 `git log` 可以看到：

    commit 8598a9ebf60afe2154823f5fad61a8b6be911bb0
    Author: TheLover_Z <sethverlo5@gmail.com>
    Date:   Tue Jun 18 23:43:20 2013 +0800

        second

    commit a51f21967c85284495342bc964ebb8261eb33058
    Author: TheLover_Z <sethverlo5@gmail.com>
    Date:   Tue Jun 18 23:40:02 2013 +0800

        first

可以看到我提交了两次，第二次的 commit 是 `8598a9ebf60afe2154823f5fad61a8b6be911bb0`，第一次的是 `a51f21967c85284495342bc964ebb8261eb33058`. 怎么这么长！其实可以只输入前 6 位的…

然后输入 `git reset a51f21` 后面这一串是第一次 commit 的代号，然后就可以看到：

    Unstaged changes after reset:
    M	a.txt

用 `git status` 可以看到：

    # On branch master
    # Changes not staged for commit:
    #   (use "git add <file>..." to update what will be committed)
    #   (use "git checkout -- <file>..." to discard changes in working directory)
    #
    #	modified:   a.txt
    #
    no changes added to commit (use "git add" and/or "git commit -a")

说明回到了提交之前，但是文件还是被修改了，这时候说不定你想修改一下别的什么东西。如果你想无条件回退回去，就把之前的 `git reset` 后面加上 `--hard` 参数，变成 `git reset --hard a51f21`，然后…boom，世界清静了：

    HEAD is now at a51f219 first

看一下 `a.txt` 的状态：

    ➜ git:(master) cat a.txt
    git is fun
    programming is fun
    computer is also fun

## 流程

有一天，小明决定和几个好基友共同参与一款开源软件的编程工作，那么完整的流程应该是这样子的：

- `git init` 创建新仓库，或者使用 `git clone` 来克隆一份现有的代码。
- 对代码进行修改，这期间你可能需要使用 `git branch` 或者 `git checkout` 来切换到某个特定的 branch 进行工作。
- `git status` 看一眼目前的状态是否正确。
- `git add` 添加所需跟踪的文件，你可能需要编辑一下 `.gitignore` 来忽略掉某些不必要的文件或者重要文件。
- `git status` 看一眼目前的状态是否正确。
- `git push` 推送过去。如果出错的话，有一个可能是因为别人已经更新过代码，而你手上的这份代码比较旧，这时候需要用 `git pull` 来更新到最新版本）。

## todo

其实还有一些之前计划的内容并没有写在这里。是因为回头看的时候，觉得会把初学者绕晕，而 *对初学者友好* 是我追求的第一要务。这些当做一个 todo 列表吧，在此列出，说不定哪天会更新。

- fork：用于没有 master 权限的时候。
- git pull 和 git merge 的区别。
- merge 的原理：虽然不想讲原理但是 merge 不知道原理的话个人认为在深入使用的过程中也许会有麻烦。
- 架设服务器和 ssh 密钥。
- 冲突处理部分太粗略。
- tags 的相关操作。

## 有问题/想吐槽？

- [Gmail](mailto:sethverlo5@gmail.com)
