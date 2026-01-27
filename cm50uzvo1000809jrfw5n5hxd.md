---
title: "The one about using factories with entities in Sinatra"
seoDescription: "Find out how to use create strategy in factories when your Ruby objects do not implement save! method"
datePublished: Mon Dec 23 2024 09:53:04 GMT+0000 (Coordinated Universal Time)
cuid: cm50uzvo1000809jrfw5n5hxd
slug: the-one-about-using-factories-with-entities-in-sinatra
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1734812714751/a2e4b95b-09b5-4e3e-b915-9a523cc9b668.png
tags: sinatra, ruby, ruby-on-rails, testing, rspec, factorybot, sinatrarb

---

I mentioned in a [previous post](https://davidmontesdeoca.dev/the-one-about-how-things-work-in-an-american-fintech) that in the project I am currently working on we do not use [Rails](https://rubyonrails.org/) in most of the applications. There are some exceptions in certain legacy applications that represent the core of the project, from which we have been progressively extracting functionalities and creating new microservices with [Sinatra](https://sinatrarb.com/).

In the case of my team, part of our focus so far has been on the new payouts application that I [also mentioned in another post](https://davidmontesdeoca.dev/the-one-about-linting-in-a-legacy-ruby-project), which allows us to automate certain payments.

In that application, [Sequel](https://sequel.jeremyevans.net/) provides the ORM layer for mapping database records to Ruby objects. We do not use [models like in Rails](https://guides.rubyonrails.org/active_record_basics.html) inheriting from `ActiveRecord`, but entities that inherit from the [Data class](https://docs.ruby-lang.org/en/3.2/Data.html), added to the Ruby core in version 3.2.

```ruby
BankAccount < Data.define(:account_code, :bank, :currency_code, :country)
```

When I joined the team, we used factories in tests with [FactoryBot](https://github.com/thoughtbot/factory_bot):

```ruby
FactoryBot.define do
  factory :bank_account, class: BankAccount do
    account_code { "CITI-00000001-USD" }
    bank { "citibank" }
    currency_code { "USD" }
    country { "US" }
  end
end
```

However, those factories were only used to generate instances of the entities:

```ruby
bank_account = FactoryBot.build(:bank_account, account_code: "CITI-00000001-EUR", currency_code: "EUR", country: "ES")
```

I was quite surprised that those factories were not used to create the necessary records in the database for every single test example.

The records were defined with the required values in a module:

```ruby
module Test
  module Helpers
    module BankAccountStorage
      def seed_bank_account_storage
        Database.connection do |db|
          db[:bank_accounts].insert(account_code: "CITI-123456789-USD", bank: "citibank", currency_code: "USD", country: "US")
          db[:bank_accounts].insert(account_code: "CITI-123456789-EUR", bank: "citibank", currency_code: "EUR", country: "ES")
          db[:bank_accounts].insert(account_code: "CITI-123456789-GBP", bank: "citibank", currency_code: "GBP", country: "GB")
          # ...more records...
        end
      end
    end
  end
end
```

> `Database.connection` is just a custom wrapper around a Sequel connection.

The module was then included in the corresponding test file:

```ruby
require "spec_helper"
require_relative "../test/helpers/bank_account_storage"

describe "POST /payments/initiate" do
  include Test::Helpers::BankAccountStorage

  before do
    seed_bank_account_storage
  end

  context "when paying in USD" do
  # ...
```

The way I see it, doing that is not maintainable and would not scale well, for several reasons:

* We are generating more records than needed for each example. Therefore, each test is slower than necessary.
    
* We do not have control over the attributes of each of the records created.
    
* We are adding indirection within each test file where that module is used, because we are asserting over values that are not defined neither in the test example nor in the test file itself.
    

I would even prefer to use **fixtures** rather than keep creating records that way, but only if I knew that the code would never change. And we all know that rarely happens.

When it was my turn to refactor a part of that application, I decided it was time to change that approach as a previous step to introducing new changes.

Of course, the first step was to delete those modules and start persisting records in the database with FactoryBot:

```ruby
bank_account = FactoryBot.create(account_code: "CITI-123456789-USD", bank: "citibank", currency_code: "USD", country: "US")
```

However, when I ran the test suite, I found errors like the following:

```plaintext
Failure/Error: bank_account = FactoryBot.create(account_code: "CITI-123456789-USD", bank: "citibank", currency_code: "USD", country: "US")

NoMethodError:
undefined method `save!' for an instance of BankAccount
# /usr/local/bundle/gems/factory_bot-6.4.6/lib/factory_bot/evaluation.rb:15:in `create'
# /usr/local/bundle/gems/factory_bot-6.4.6/lib/factory_bot/strategy/create.rb:12:in `block in result'
# /usr/local/bundle/gems/factory_bot-6.4.6/lib/factory_bot/strategy/create.rb:9:in `result'
# /usr/local/bundle/gems/factory_bot-6.4.6/lib/factory_bot/factory.rb:43:in `run'
# /usr/local/bundle/gems/factory_bot-6.4.6/lib/factory_bot/factory_runner.rb:29:in `block in run'
# /usr/local/bundle/gems/factory_bot-6.4.6/lib/factory_bot/factory_runner.rb:28:in `run'
# /usr/local/bundle/gems/factory_bot-6.4.6/lib/factory_bot/strategy_syntax_method_registrar.rb:28:in `block in define_singular_strategy_method'
# ...
```

Of course, I have always used FactoryBot in Rails applications where each factory is associated with models inheriting from `ActiveRecord`.

Our entities do not implement the `save!` method, which is the [default implementation](https://thoughtbot.github.io/factory_bot/ref/build-and-create.html#to_create) of the `to_create` method:

```ruby
to_create { |obj, context| obj.save! }
```

Therefore, I had to [override that behavior](https://github.com/thoughtbot/factory_bot/blob/main/GETTING_STARTED.md#custom-methods-to-persist-objects) for all our factories.

In this case, I did it in the FactoryBot support file:

```ruby
require "factory_bot"

RSpec.configure do |config|
  config.before(:suite) do
    FactoryBot.find_definitions
    FactoryBot.define do
      initialize_with { new(**attributes) }

      to_create do |obj, context|
        Database.connection do |db|
          if !context.respond_to?(:table_name)
            raise NotImplementedError,
              "define a transient table_name attribute inside the factory with the name of the database table"
          end

          db[context.table_name.to_sym].insert(obj.to_h)
        end
      end
    end
  end
end
```

In the factory we define the transient attribute `table_name`:

```ruby
FactoryBot.define do
  factory :bank_account, class: BankAccount do
    transient do
      table_name { BankAccountRepository::TABLE_NAME }
    end

    account_code { "CITI-00000001-USD" }
    bank { "citibank" }
    currency_code { "USD" }
    country { "US" }
  end
end
```

Since it is related to persistence, I decided to define the constant with the name of the table in the corresponding repository:

```ruby
class BankAccountRepository
  TABLE_NAME = :bank_accounts
  # ...
```

And that is all. With those simple changes we already have at our disposal all the power that `FactoryBot.create` offers.

It is important that the error message that would appear in case of using the *create* strategy without having the transient attribute `table_name` defined, allow anyone to fix that error very quickly.

I must say that the team welcomed the new changes with open arms.

Thank you for reading and see you in the next one!

---

%%[buy-me-a-coffee]