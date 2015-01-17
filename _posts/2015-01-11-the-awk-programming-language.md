---
layout: post
title: "The Awk Programming Language 笔记"
description: ""
category: "note"
tags: ['awk']
---
{% include JB/setup %}

# The Awk Programming Language 笔记

在 [V2EX](http://www.v2ex.com/t/160899) 看到这个帖子以后，就想着把 Awk 或者 Sed 完整地学一遍。然后就找到了 [The AWK Programming Language](http://book.douban.com/subject/1876898/) 这本书…

## input 和 field

运行 awk 可以使用三种方法，第一种是：

    `awk 'program' input files`

这里 `files` 当然可以是多个文件，比如说 `awk '$3 == 0 { print $1 }' file1 file2`

或者手动输入文本：

    `awk 'program'`

然后输入的文本都会被用来匹配，直到碰到 EOF 结束

或者使用 `-f` 来指定代码存放的文件：

    awk -f progfile optional list ofinput files

在 Awk 中，**只有** 两种数据类型，一种是数字，另一种就是字符串了。

Awk 每次读一行数据，然后分割成不同的 field, 比如说 `$1` 就代表了第一个 field，`$2` 代表了第二个，以此类推。值得注意的是 `$0` 代表了整行数据。

有几个特殊的用法，比如说 `NF` 代表 'Number of Fields'，即每行数据有多少个 `field`. `NR` 代表 'Number of lines Read so far', 即 当前读进来了多少行。

## printf

`print` 只能用来输出一些简单的格式，而 `printf` 可以输出一些比较复杂的格式。比如说：

    { printf("total pay for %s is $%.2f\n", $1, $2 * $3) }

## selection

selection 就是 `pattern { action }` 中的 `pattern`，可以通过三种方法来进行选择：

- 根据比较来选择：`$2 >= 5`
- 根据计算来选择：`$2 * $3 > 50`
- 根据文本来选择：`$1 == "Susie"`，当然也可以用正则来匹配

## BEGIN 和 END

`BEGIN` 在所有规则之前运行，`END` 在所有规则之后运行，比如说书里这个例子，用 `BEGIN` 给表格添加表头：

    BEGIN { print "NAME RATE HOURS"; print "" }
    { print }

输出是：

    NAME RATE HOURS
    Beth 4.00 0
    Dan 3.75 0
    Kathy 4.00 10
    Mark 5.00 20
    Mary 5.50 22
    Susie 4.25 18

## computing with awk

比较神奇的是，Awk 的变量不需要初始化…比如说 `{ emp = emp + 1 }` 就可以直接写。

字符串的拼接也有点儿奇怪，是 `{ names = names $1 " " }` 这样的形式而不需要使用 `+` 来拼接，也不需要逗号。

## 比较有用的「一行流」

### 总行数

    END { print NR }

### 第 10 行

    NR == 10

### 每行最后一个元素

    { print $NF }

### 包含 Beth 的行数

    /Beth/ { nlines = nlines + 1 }
    END { print nlines }

### 超过 80 个字符的行

    length($0) > 80

### 删掉第二列以后输出行

    { $2 = ""; print }

### 倒序输出每行

    { for (i = NF; i > 0; i = i - 1) printf("%s " $i)
      printf("\n" )
    }

