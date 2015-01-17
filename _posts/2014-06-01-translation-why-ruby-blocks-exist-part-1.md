---
layout: post
title: "翻译：Why Ruby Blocks Exist, Part 1"
description: ""
category: "translations"
tags: ["ruby"]
---
{% include JB/setup %}

# 翻译：Why Ruby Blocks Exist, Part 1

探索 Ruby 中 `each` 方法

原文作者：[Jay McGavren](http://programming.oreilly.com/2014/02/why-ruby-blocks-exist.html)

译者：[TheLover_Z](http://theloverz.me)

越来越多的语言都开始或多或少地开始支持闭包了（甚至 Java 也加入了这场游戏）。Ruby 在很早的时候就有一种叫 *block* 的类似闭包的结构，block 是构成 Ruby 的很重要的一部分。使用恰当的话，block 可以削减重复代码甚至可以让代码更少错误。理解 block 可以让你充分理解编程语言。

Ruby 的很多方法都用到了 block，但我今天会挑选一个特别的进行讲解，即 `each`. 我们可以看到一些重复的代码，在许多语言中都很难重构。但是使用了 `each`, 我们可以迅速地把重复代码消灭掉。

## 重复循环

假如说有一个客户让我们帮忙实现一个发票系统。第一个需求是可以取得某订单所有的价格然后输出。

如下的代码可以为一份发票生成一组价格：

    item_prices = [3, 24, 9]

我们需要定义一个方法，可以从上面的数组统计出总价。在大多数语言中，我们 *只能* 遍历整个数组，然后把它们加起来。

    def total(prices)
      amount = 0
      index = 0
      while index < prices.length
        amount += prices[index]
        index += 1
      end
      return amount
    end
     
    puts total(item_prices)

输出：

    36

如果你是从其它语言过来的，Ruby 的语法看起来应该不会特别奇怪。方法在 `def` 和 `end` 中定义。循环在 `while` 和 `end` 中定义，然后当我们传给循环最后一个元素的序列之后就可以退出循环。`+=` 操作符给一个已存在的变量增加一定的数值。

现在我们来写第二个退款的方法。它需要遍历发票的价格，然后从客户的账户中减去对应的价格。

    def refund(prices)
      amount = 0
      index = 0
      while index < prices.length
        amount -= prices[index]
        index += 1
      end
      amount
    end
     
    puts refund(item_prices)

输出

    -36

`refund` 方法和 `total` 方法极其类似，只不过是加上和减去的区别。

我们需要第三个方法，这个方法给每个物品都打折 1/3，并且输出节省的金钱。

    def show_discounts(prices)
      index = 0
      while index < prices.length
        amount_off = prices[index] / 3
        puts "Your discount: $#{amount_off}"
        index += 1
      end
    end
     
    show_discounts(item_prices)

输出：

    Your discount: $1
    Your discount: $8
    Your discount: $3

在这三个方法中，有许多重复的代码，并且都和循环有关。这绝对不符合 DRY(Don’t Repeat Yourself) 原则。如果我们可以把重复代码抽出去到另外一个方法，然后让 `total`, `refund`, `show_discounts` 来调用就好了。

    def do_something_with_every_item(array)

      # Set up total or refund variables here,
      # IF we need them.

      index = 0
      while index < array.length

        # Add to the total OR
        # Add to the refund OR
        # Show the discount

        index += 1
      end
    end

问题是：在运行循环之前，你应该怎么声明你需要的变量？并且在循环中你如何执行你需要的代码？

## Blocks

我们要是能把一大坨要执行的代码传进正在运行的方法就好了，那样我们的问题就解决了。我们可以使用 `do_something_with_every_item` 来控制循环，然后传给它增加或者减少或者计算折扣的代码。很遗憾，很多语言都没有一个很好的解决方案。

可是 Ruby 有 block! block 基本上就是和方法调用相关联的一坨代码。当方法运行的时候，它可以调用 block 一次或者多次。

使用 `yield` 关键词就可以声明一个接受 block 的方法。如果你要向 block 传一个或者多个参数，把它们看作 `yield` 的参数就可以了。

    def calculate_tax(income)
      tax_rate = 0.2
      yield income * tax_rate
    end

当你调用你的方法的时候，你可以把你想要的代码提供给 block. block 挂在方法参数的后面，看起来像是附加的参数。

![Ruby Blocks](/images/ruby_blocks/block.png)

我们来看看调用 block 的方法：

    income = 60000
    net_income = income
     
    calculate_tax(income) do |tax|
      puts "You owe #{tax}."
      net_income -= tax
    end
     
    puts "Your net income: #{net_income}"

输出：

    You owe 12000.0.
    Your net income: 48000.0

当 Ruby 在执行方法的过程中碰到了 `yield` 关键词的时候，它会把控制权从方法移交到 block. 传给 `yield` 的参数会放在竖线（即 `|tax|`）包起来的 block 参数中。需要注意的是参数只在 block 中存活。

## 使用 blocks 清理代码

现在我们了解 Ruby block 了，我们写一个 `do_something_with_every_item` 方法来清理重复的代码。

    def do_something_with_every_item(array)
        index = 0
        while index < array.length
        yield array[index]
        index += 1
        end
    end

就是这样啦！这个方法会查看数组中的每个元素。每次碰到 `yield` 关键词的时候，方法都会把当前的元素传递给 block 作为参数。

现在，我们可以进行重构了。

    def total(prices)
      amount = 0
      do_something_with_every_item(prices) do |price|
        amount += price
      end
      return amount
    end
     
    def refund(prices)
      amount = 0
      do_something_with_every_item(prices) do |price|
        amount -= price
      end
      return amount
    end
     
    def show_discounts(prices)
      do_something_with_every_item(prices) do |price|
        amount_off = price / 3
        puts "Your discount: $#{amount_off}"
      end
    end
     
    order = [3, 24, 6]
    puts total(order)
    puts refund(order)
    show_discounts(order)

输出：

    33
    -33
    Your discount: $1
    Your discount: $8
    Your discount: $2

重点来了：你的新方法并不局限于数字！你可以传给它一个字符串数组：

    def fix_names(names)
      do_something_with_every_item(names) do |name|
        puts name.capitalize
      end
    end
     
    fix_names ['anna', 'joe', 'zeke']

输出：

    Anna
    Joe
    Zeke

或者 `Time` 实例的数组：

    def get_years(times)
      do_something_with_every_item(times) do |time|
        puts time.year
      end
    end
     
    get_years [Time.at(0), Time.now, Time.now + 31536000]

输出：

    1969
    2014
    2015

或者其它任何你能想到的！

## each 方法

其实，我们实现的方法在 Ruby 数组类中已经有实现了，它叫做 `each`.

我们来看看使用 `each` 重写的 `total`, `fix_names` 和 `get_years` 方法。

    def total(prices)
      amount = 0
      prices.each do |price|
        amount += price
      end
      return amount
    end
     
    def fix_names(names)
      names.each do |name|
        puts name.capitalize
      end
    end
     
    def get_years(times)
      times.each do |time|
        puts time.year
      end
    end

调用的方法和之前一样：

    puts total([3, 24, 6])
    fix_names ['anna', 'joe', 'zeke']
    get_years [Time.at(0), Time.now, Time.now + 31536000]

`each` 方法让我们得以削减代码中的重复部分。稍后我们会看到更多激动人心的用法。

[youtube](https://www.youtube.com/watch?v=R_cCtEiZcxY)

如果你想更深入了解 block，可以看一下这个视频。它会告诉你如何设置 Ruby 环境，然后带你尝试 `each` 和其它方法。
