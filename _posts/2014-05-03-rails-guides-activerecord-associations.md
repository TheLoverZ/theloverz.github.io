---
layout: post
title: "Rails Guides 阅读计划：ActiveRecord Associations"
description: ""
category: "note"
tags: ['Ruby on Rails']
---
{% include JB/setup %}

# Rails Guides 阅读计划：ActiveRecord Associations

作者：[TheLover_Z](http://theloverz.me)

## belongs_to

`belongs_to` 维护了两个 model instance 之间的 primary key 的关系。

在看 guides 的时候我观察到一个我不太熟悉的用法，见下面代码：

    class CreateOrders < ActiveRecord::Migration
        create_table :orders do |t|
          t.belongs_to :customer
          t.datetime :order_date
          t.timestamps
        end
      end
    end

注意到 `t.belongs_to :customer` 这一行，我之前没有用过，我一般都是会手动创建一个 `customer_id`。我试了一下，这样的用法在 `schema` 中会自动生成类似于 `t.integer  "customer_id"` 的代码。所以这样还是挺方便的。

## has_many :through

`has_many :through` 一般用于多对多的关系，并且跟第三个 model 也有关联。guides 中举的例子是 Physician 和 Patient 通过 Appointment 来建立关系。有了这样的认识 `has_many :through` 就好理解了，直接抄代码：

    class Physician < ActiveRecord::Base
      has_many :appointments
      has_many :patients, through: :appointments
    end
     
    class Appointment < ActiveRecord::Base
      belongs_to :physician
      belongs_to :patient
    end
     
    class Patient < ActiveRecord::Base
      has_many :appointments
      has_many :physicians, through: :appointments
    end

## belongs_to 和 has_one

区别一方面在外键，另一方面是语义上的区别。比如说一般来说「供应商 has_one 账户」更自然一点。

## polymorphic associations

见之前的 [post](/note/2014/04/29/rails-guides-activerecord-basics/).

## self joins

简单来说就是和自己建立关系，比如说你想在一个数据库 model 中存储所有的雇员（employee），也想可以追踪经理（manager）和下级（subordinate）的关系。

可以这么写：

    class Employee < ActiveRecord::Base
      has_many :subordinates, class_name: "Employee",
                              foreign_key: "manager_id"
     
      belongs_to :manager, class_name: "Employee"
    end

然后就可以使用 `@employee.subordinates` 和 `@employee.manager`.

## controlling caching

一句话不割：ActiveRecord 默认会把所有的 SQL 结果存起来以便下次使用，但是会造成一些数据不同步的问题，可以使用 `customer.orders(true).foobar`，和 `reload` 一样的效果。

## join table

字母顺序，比如说应该是 `customer_order` 而不是 `order_customer`.

## build_association 和 create_association

- `build_association(attr)` 会进行初始化，外键也会设置，但是不会存储。
- `create_association` 会进行存储。
- `create_association!` 在参数非法的时候抛出异常。

## counter_cache

形如 `@customer.orders.size` 这样的语句会向 SQL 进行 `COUNT(*)` 查询，可以使用 `counter_cache` 来避免，用法：

    class Order < ActiveRecord::Base
      belongs_to :customer, counter_cache: true
    end
    class Customer < ActiveRecord::Base
      has_many :orders
    end

## includes

其实挺好懂的，如果有这样的 Model: 

    class LineItem < ActiveRecord::Base
      belongs_to :order
    end
     
    class Order < ActiveRecord::Base
      belongs_to :customer
      has_many :line_items
    end
     
    class Customer < ActiveRecord::Base
      has_many :orders
    end

然后呢你经常要进行 `@line_item.order.customer` 这样的操作，就可以改成这样：

    class LineItem < ActiveRecord::Base
      belongs_to :order, -> { includes :customer }
    end

## association callbacks

有四种：

- before_add
- after_add
- before_remove
- after_remove

