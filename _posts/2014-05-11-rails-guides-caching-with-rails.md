---
layout: post
title: "Rails Guides 阅读计划：Caching with Rails"
description: ""
category: "note"
tags: ['Ruby on Rails']
---
{% include JB/setup %}

# Rails Guides 阅读计划：Caching with Rails

作者：[TheLover_Z](http://theloverz.me)

这个 **不是翻译**，只是总结一下自己觉得重要的或者有意思的东西。

## Basic Caching

有三种：Page，Action 和 Fragment. 默认的是 Fragment Caching，如果想要使用 Page Caching 和 Action Caching 的话需要添加 Gem `actionpack-page_caching` 和 `actionpack-action_caching`.

如果是开发环境，要确保 `config.action_controller.perform_caching` 设置为 `true`.

### Page Caching

简单来说就是把页面转换成静态的（放在 `public` 文件夹下），所以根本不经过 rails 栈，因为也会非常非常快。但是比如说需要验证的情况就不适用了，而且过期也是个需要处理的问题。

在 Rails 4 中被移除。

### Action Caching

比 Page Caching 好一点，相当于一个 `before_filter`，要经过 rails 栈。

在 Rails 4 中被移除。

### Fragment Caching

重点~~~（请做好笔记）~~~。因为 Web 其实就是一块儿一块儿的东西拼接起来的（想想 `partial`），所以 Fragment Caching 比较常用。

Fragment Caching 主要是把一块儿 view 的逻辑包起来（用 block），然后再次请求的时候就可以直接读缓存了。

大概是这个样子的：

    <% cache do %>
      All available products:
      <% Product.all.each do |p| %>
        <%= link_to p.name, product_url(p) %>
      <% end %>
    <% end %>

这里的 `cache` block 就是 Fragment Caching.

但是我想在一个 action 里面绑定多个 cache 怎么办呢？用 `action_suffix`:

    <% cache(action: 'recent', action_suffix: 'all_products') do %>

设置过期用 `expire_fragment`:

    expire_fragment(controller: 'products', action: 'recent', action_suffix: 'all_products')

上面这些用法可以看到，我们先绑定了一个 cache block 然后再进行调用，如果不想这么麻烦怎么办？给 `cache` block 指定一个 `key`:

    <% cache('all_available_products') do %>
      All available products:
    <% end %>

过期的话使用 `expire_fragment('all_available_products')`.

如果觉得手动过期太麻烦，那你就需要一个 helper 了：

    module ProductsHelper
      def cache_key_for_products
        count          = Product.count
        max_updated_at = Product.maximum(:updated_at).try(:utc).try(:to_s, :number)
        "products/all-#{count}-#{max_updated_at}"
      end
    end

`cache_if` 和 `cache_unless` 顾名思义不解释。

Active Record model 也可以当做 key 来用：

    <% Product.all.each do |p| %>
      <% cache(p) do %>
        <%= link_to p.name, product_url(p) %>
      <% end %>
    <% end %>

cache 还可以嵌套：

    <% cache(cache_key_for_products) do %>
      All available products:
      <% Product.all.each do |p| %>
        <% cache(p) do %>
          <%= link_to p.name, product_url(p) %>
        <% end %>
      <% end %>
    <% end %>

### SQL caching

一行流：想想为什么要 `reload` 就好了。

## Cache Stores

有好几种存储 Cache 的方式，不过需要注意的是 Page Caching 永远存储在磁盘上。

配置方式是设置类似 `config.cache_store = :memory_store` 这样的东西。

### ActiveSupport::Cache::Store

这是 Cache 的基础。

可以调用的方法包括 `read`, `write`, `delete`, `exist?`, `fetch`. 

`fetch` method 带一个 block，要么返回已有的值，要么这个值不存在，就返回 block.

还有一些参数：

- `:namespace`: namespace 不解释
- `:compress`: 是否压缩
- `:compress_threshold`: 小于多少就不压缩了，默认 16k.
- `:expires_in`: 过期时间
- `:race_condition_ttl`: 如果设置了 `:expires_in` 那么建议设置一下这个，可以参见[dog pile effect](http://caiknife.github.io/blog/2013/11/20/how-to-deal-with-dog-pile-effect/).

### ActiveSupport::Cache::MemoryStore

存储在和 Ruby 进程相同的内存中。默认 32Mb 但是可以设置：

    config.cache_store = :memory_store, { size: 64.megabytes }

但是如果运行了多个 Rails 进程的话这些东西是不能共享的。

### ActiveSupport::Cache::FileStore

存储在文件中：

    config.cache_store = :file_store, "/path/to/cache/directory"

这是默认的方式。

### ActiveSupport::Cache::MemCacheStore

这个就是用 redis 神马的了。

### ActiveSupport::Cache::EhcacheStore

JRuby 可以考虑这个。

### ActiveSupport::Cache::NullStore

顾名思义，所有的 `fetch` 和 `read` 会 miss.

### Custom Cache Stores

自定义的话这么搞：`config.cache_store = MyCacheStore.new`.

## Conditional GET support

意思是如果自从上次 GET 请求以后，没有什么更改，那么就可以直接返回上次的结果。

大概是这样的：

    class ProductsController < ApplicationController

      def show
        @product = Product.find(params[:id])

        if stale?(last_modified: @product.updated_at.utc, etag: @product.cache_key)
          respond_to do |wants|
            # ... normal response processing
          end
        end
      end
    end

如果根据时间戳和 etag 来判断得到一个请求是 stale 的，那么就会执行 block 中的内容；如果不是，那么什么都不用做，因为 rails 已经帮你做好了一切。

或者还可以传进去一个 model，rails 会用 `updated_at` 和 `cache_key` 来设置 `last_modified` 和 `etag`.

    class ProductsController < ApplicationController
      def show
        @product = Product.find(params[:id])
        respond_with(@product) if stale?(@product)
      end
    end

如果你用的都是默认的处理过程的话（没有做什么特殊处理），那么就更简单了。可以使用 `fresh_when`:

    class ProductsController < ApplicationController

      # This will automatically send back a :not_modified if the request is fresh,
      # and will render the default template (product.*) if it's stale.

      def show
        @product = Product.find(params[:id])
        fresh_when last_modified: @product.published_at.utc, etag: @product
      end
    end

如果这个请求是新的，那么 Rails 会自动设置 `:not_modified`；否则会 `render` 默认的 template.
