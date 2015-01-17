---
layout: post
title: "Rails Guides Translation Associations"
description: ""
category: "translations"
tags: ['Ruby on Rails', 'translations']
---
{% include JB/setup %}

# Active Record 数据表关联（待续）

译者：[TheLover_Z](http://theloverz.me/)

这份文档讲述了 Active Record 的关联（association）特性。读完这篇文档以后，你会了解到：

- 如何声明 Active Record 模型之间的关联。
- 如何理解不同类型的 Active Record 关联。
- 如何通过创建关联来向你的 model 添加方法。

## 1. 为什么使用关联？

为何我们需要 model 之间的关联？因为关联可以使你代码中的一些通用操作更加简单和清楚。比如说，考虑一个简单的 Rails 项目，这个项目包含一个针对顾客的 model 和一个针对订单的 model. 每一个顾客都可以有许多订单。如果不使用关联的话，model 声明应该像下面这样：

  class Customer < ActiveRecord::Base
  end

  class Order < ActiveRecord::Base
  end

现在，假定我们要给一个已经存在的顾客添加一个新的订单。我们可能需要这么做：

  @order = Order.create(order_date: Time.now, customer_id: @customer.id)

或者在删除一个顾客的时候，我们要确保这位顾客所有的订单也被删除掉：

  @order = Order.where(customer_id: @customer.id)
  @order.each do |order|
    order.destroy
  end
  @customer.destroy

现在有了关联，我们可以简化这个流程。只要告诉 Rails 在两个 model 之间有关联就可以了。改进后的代码如下，先把两个 model 进行关联：

  class Customer < ActiveRecord::Base
    has_many :orders, dependent: :destroy
  end

  class Order < ActiveRecord::Base
    belongs_to :customer
  end

有了这样的声明以后，为特定顾客创建一个新订单就更容易了：

  @order = @customer.orders.create(order_date: Time.now)

删除一个顾客和所有相关的账单就变得 *异常* 简单：

  @customer.destroy

想要了解关联的不同类型，请继续读下面的部分。会有一些关于关联的小贴士和小技巧，然后会有一份完整的参考。

## 2. 关联的类型

在 Rails 中，一个 *关联* 是指两个 Active Record model 之间的关系。关联使用宏风格调用（macro-style call）来实现，所以你可以用声明式的方法（declaratively）给你的 model 添加特性。举个例子，通过声明一个 model `belongs_to` 另外一个，你就是在告诉 Rails 需要包含两个 model 实例的主键-外键信息，并且你也得到了许多附加方法。Rails 支持如下 6 种关联：

- belongs_to
- has_one
- has_many
- has_many :through
- has_one :through
- has_and_belongs_to_many

这份文档的其余部分，你将学会如何声明和使用不同的关联。但是首先，我们来迅速概括一下各种关联所适用的场景。

### 2.1 `belongs_to` 关联

`belongs_to` 关联设定了两个 model 之间一对一的关系，使得当前 model 的每个实例都「归属于」另外一个 model 的实例。比如说，如果你的项目包含顾客和订单，每个订单都只能归属于一位顾客，你就可以这样声明：

    class Order < ActiveRecord::Base
      belongs_to :customer
    end

![belongs_to](/images/rails_guides_translations/belongs_to.png)

注意：`belongs_to` 关联 *必须* 使用单数形式。如果你使用了复数形式，那么 Rails 就会告诉你「uninitialized constant Order::Customers」. 这是因为 Rails 会自动查找类名。如果你使用了错误的复数形式，那么 Rails 将会找不到这个类。

相应的 migration 就会像是这样：

    class CreateOrders < ActiveRecord::Migration
      def change
        create_table :customers do |t|
          t.string :name
          t.timestamps
        end
 
        create_table :orders do |t|
          t.belongs_to :customer
          t.datetime :order_date
          t.timestamps
        end
      end
    end

### 2.2 `has_one` 关联

`has_one` 关联仍然是一个一对一的关系，但是有一些语义上的不同。这个关联指定了 model 的每一个实例包含或者说拥有另外一个 model 的实例。比如说，如果在你的项目中，每一个供应商都只能拥有一个账户，那么你就应该这么声明：

    class Supplier < ActiveRecord::Base
      has_one :account
    end

![has_one](/images/rails_guides_translations/has_one.png)

对应的 migration 应该是这个样子：

    class CreateSuppliers < ActiveRecord::Migration
      def change
        create_table :suppliers do |t|
          t.string :name
          t.timestamps
        end
     
        create_table :accounts do |t|
          t.belongs_to :supplier
          t.string :account_number
          t.timestamps
        end
      end
    end

### 2.3 `has_many` 关联

`has_many` 关联指定了一对多的关系。你经常可以发现这个关联和 `belongs_to` 是天生一对。这个关联指定了一个 model 的每个实例都有零个或者多个来自其他 model 的实例。比如说，项目中包含顾客和订单，顾客 model 的声明应该是这样的：

    class Customer < ActiveRecord::Base
      has_many :orders
    end

注意：声明 `has_many` 的时候 model 需要是复数形式。

![has_many](/images/rails_guides_translations/has_many.png)

对应的 migration 应该是这个样子：

    class CreateCustomers < ActiveRecord::Migration
      def change
        create_table :customers do |t|
          t.string :name
          t.timestamps
        end
     
        create_table :orders do |t|
          t.belongs_to :customer
          t.datetime :order_date
          t.timestamps
        end
      end
    end

### 2.4 `has_many :through` 关联

`has_many :through` 关联通常被用来和另一个 model 建立多对多的关系。这个关联指明了声明关联的 model 可以 *通过* 第三方 model 来与另一个 model 的零个或者多个实例相匹配。比如说，考虑一个医患系统，患者可以预约医生。相关的关联声明可能像下面这样：

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

![has_many_through](/images/rails_guides_translations/has_many_through.png)

对应的 migration 应该是这个样子：

    class CreateAppointments < ActiveRecord::Migration
      def change
        create_table :physicians do |t|
          t.string :name
          t.timestamps
        end
     
        create_table :patients do |t|
          t.string :name
          t.timestamps
        end
     
        create_table :appointments do |t|
          t.belongs_to :physician
          t.belongs_to :patient
          t.datetime :appointment_date
          t.timestamps
        end
      end
    end

连接模型（join models）的集合操作可以通过 API 来进行。比如说，如果你使用：

    physician.patients = patients

new join models are created for newly associated objects, and if some are gone their rows are deleted.

WARNING: 连接模型的删除操作是直接进行的，并不会触发回调。

`has_many :through` 关联在通过嵌套的 `has_many` 设置「快捷方式」的时候也很有用。比如说，如果一个文档有很多部分，每一个部分又有许多段落，有时候你想得到所有段落的集合，那么你就可以这么做：

    class Document < ActiveRecord::Base
      has_many :sections
      has_many :paragraphs, through: :sections
    end
     
    class Section < ActiveRecord::Base
      belongs_to :document
      has_many :paragraphs
    end
     
    class Paragraph < ActiveRecord::Base
      belongs_to :section
    end

通过 `through : :sections`，Rails 现在就可以理解：

    @document.paragraphs

### 2.5 `has_one :through` 关联

`has_one :through` 关联建立了和另一个 model 的一对一的关系。这个关联指明了一个 model 可以通过使用 `through` 来指定第三方 model，进而和另一个 model 的实例进行匹配。比如说，如果每个供应商都有一个账号，每个账号都有一个账户清单，那么顾客 model 就可以这么写：

    class Supplier < ActiveRecord::Base
      has_one :account
      has_one :account_history, through: :account
    end
     
    class Account < ActiveRecord::Base
      belongs_to :supplier
      has_one :account_history
    end
     
    class AccountHistory < ActiveRecord::Base
      belongs_to :account
    end

![has_one_through](/images/rails_guides_translations/has_one_through.png)

对应的 migration 应该是这个样子：

    class CreateAccountHistories < ActiveRecord::Migration
      def change
        create_table :suppliers do |t|
          t.string :name
          t.timestamps
        end
     
        create_table :accounts do |t|
          t.belongs_to :supplier
          t.string :account_number
          t.timestamps
        end
     
        create_table :account_histories do |t|
          t.belongs_to :account
          t.integer :credit_rating
          t.timestamps
        end
      end
    end

### 2.6 `has_and_belongs_to_many` 关联

`has_and_belongs_to_many` 关联不需要第三方 model 就可以在两个 model 之间创建多对多的关系。比如说，如果你的项目包含集合（assemblies）和部分（parts），对于每个集合来说都由许多部分组成，每个部分也可以出现在许多集合中，你可以这么写：

    class Assembly < ActiveRecord::Base
      has_and_belongs_to_many :parts
    end
     
    class Part < ActiveRecord::Base
      has_and_belongs_to_many :assemblies
    end

![habtm](/images/rails_guides_translations/habtm.png)

对应的 migration 应该是这个样子：

    class CreateAssembliesAndParts < ActiveRecord::Migration
      def change
        create_table :assemblies do |t|
          t.string :name
          t.timestamps
        end
     
        create_table :parts do |t|
          t.string :part_number
          t.timestamps
        end
     
        create_table :assemblies_parts do |t|
          t.belongs_to :assembly
          t.belongs_to :part
        end
      end
    end

### 2.7 我应该选择 `belongs_to` 还是 `has_one` ?

如果你想在两个 model 中建立一对一的关系，那么你就应该在一个 model 中使用 `belongs_to`，在另一个 model 中使用 `has_one`. 但你怎么知道该使用哪个？

区别在于，



