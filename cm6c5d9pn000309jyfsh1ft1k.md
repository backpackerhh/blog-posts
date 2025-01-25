---
title: "The one about learning Ruby"
seoDescription: "Guide for beginners and experienced developers that would like to start learning Ruby"
datePublished: Sat Jan 25 2025 12:08:35 GMT+0000 (Coordinated Universal Time)
cuid: cm6c5d9pn000309jyfsh1ft1k
slug: the-one-about-learning-ruby
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1737487504845/5aa24c3c-3b31-4dab-9b10-db7981d4b53c.png
tags: ruby, rails, ruby-on-rails, backend, learning, rubyonrails, training, sinatrarb, hanamirb

---

I started working with [Ruby](https://www.ruby-lang.org/en/) in early 2010, on projects developed with [Ruby on Rails](https://rubyonrails.org/). My mentor was influential enough to convince our bosses that this programming language and this framework were the best choice for future projects. We usually worked with [CakePHP](https://cakephp.org/), so understanding how Rails works with the [MVC pattern](https://developer.mozilla.org/en-US/docs/Glossary/MVC) was really a piece of cake. Got it? Never mind...

15 years later, I work at [SNGULAR](https://www.sngular.com/), which is a consulting firm that mostly works with [Java](https://www.java.com/en/) and its ecosystem in the backend of their software development projects. They also have projects with popular languages such as [PHP](https://www.php.net/) or [Node.js](https://nodejs.org/en), among others. Focusing exclusively on what they call the **Backend chapter**, each of these technologies has an internal **learning path** defined. This is not the case with Ruby, since they do not have many projects where this is the main programming language.

When I started working at this company I was already assigned to a project, but as I already had booked my vacations, [my start at the American fintech](https://blog.davidmontesdeoca.es/the-one-about-my-new-job) was delayed for a few weeks until I was back.

I had several weeks ahead of me before leaving, so in addition to completing my onboarding, my boss proposed to do a **Ruby training** to some colleagues who did not have a project assigned at the time. All of them were experienced developers who were used to work mainly with Java or PHP, but had no previous experience with Ruby. I accepted without hesitation.

You will find below the guide I shared with the company to learn Ruby, to which I have been adding some stuff that I consider interesting during the last months.

## Ruby Training

### Recommendations

The most important recommendation is always **learn Ruby first and then learn the framework of your choice if needed**, usually the most popular. This is applicable to any ecosystem. That way, when you work on a gem, a script or anything else where you do not have available any of the libraries provided by the framework, you will be able to work with Ruby pretty easily.

Even if you already have experience with this language, I recommend you to access [this quiz](https://railshurts.com/frames/quiz/) and you will probably understand what I mean.

Another recommendation that I consider important is that you look at the version of Ruby used in the articles and documentation that you find, because some things might be outdated. On the [official website](https://www.ruby-lang.org/en/) you will always find news about the latest versions released.

### Documentation

* [Ruby documentation](https://docs.ruby-lang.org/en/)
    
* [Ruby from other languages](https://www.ruby-lang.org/en/documentation/ruby-from-other-languages/)
    
* [The Ruby Reference](https://rubyreferences.github.io/rubyref/)
    
* [Learn Ruby](https://learnxinyminutes.com/ruby/)
    

### References in the community

Not an exhaustive list and in no particular order:

* [Yukihiro Matsumoto "Matz"](https://en.wikipedia.org/wiki/Yukihiro_Matsumoto)
    
* [Sandi Metz](https://sandimetz.com/)
    
* [Janko Marohnić](https://janko.io/)
    
* [Nate Berkopec](https://www.nateberkopec.com/)
    
* [Avdi Grimm](https://avdi.codes/)
    
* [Katrina Owen](http://kytrinyx.com/)
    
* [Aaron Patterson](https://tenderlovemaking.com/)
    
* [Mike Perham](https://www.mikeperham.com/)
    
* [Akira Matsuda](https://github.com/amatsuda)
    
* [Rafael França](https://github.com/rafaelfranca)
    
* [DHH](https://dhh.dk/)
    
* [Thoughtbot](https://thoughtbot.com/blog)
    
* [Evil Martians](https://evilmartians.com/chronicles)
    
* [Shopify Engineering](https://shopify.engineering/authors/shopify-engineering)
    
* [GitHub](https://github.blog/tag/ruby/)
    

### Key concepts

Going beyond the most basic concepts present in every programming language we can find the following concepts:

* [Global Interpreter Lock (GIL)](https://en.wikipedia.org/wiki/Global_interpreter_lock):
    
    * Concurrency vs parallelism
        
* [YJIT](https://shopify.engineering/ruby-yjit-is-production-ready):
    
    * Ruby is an interpreted language
        
    * The JIT allows to dynamically compile Ruby code into machine code at runtime without interpretation
        
    * Improve performance and memory usage
        
* Types:
    
    * Duck typing vs `is_a?`/`kind_of?` vs `instance_of?`
        
    * There are **no interfaces**
        
    * Type checkers:
        
        * Not very extended in the Ruby community
            
        * [Sorbet](https://sorbet.org/)
            
        * [RBS](https://github.com/ruby/rbs)
            
* Debugging:
    
    * `<object>.methods.sort`: what methods an object respond to.
        
    * `<object>.method(:<method_name>).source_location`: where is defined a certain method.
        
    * `<object>.method(:<method_name>).super_method.source_location`: where is defined certain method with the same name in one of the ancestors.
        
* Metaprogramming:
    
    * `define_method`
        
    * [Always define respond\_to\_missing? when overriding method\_missing](https://thoughtbot.com/blog/always-define-respond-to-missing-when-overriding)
        
    * `send` vs `public_send`
        
    * Monkeypatching
        
* [Exceptions](https://www.exceptionalcreatures.com/guides/what-are-ruby-exceptions.html):
    
    * [The class hierarchy](https://www.exceptionalcreatures.com/guides/what-are-ruby-exceptions.html#the-class-hierarchy)
        
* [Pattern matching](https://docs.ruby-lang.org/en/3.4/syntax/pattern_matching_rdoc.html)
    
* [Shorthand hash syntax](https://dev.to/baweaver/ruby-3-1-shorthand-hash-syntax-first-impressions-19op)
    
* [Safe navigation operator (&)](https://thoughtbot.com/blog/ruby-safe-navigation)
    
* Strings (`to_s`) vs symbols (`to_sym`):
    
    * [Frozen strings](https://ruby-doc.org/3.3.3/syntax/comments_rdoc.html#label-frozen_string_literal+Directive)
        
* Classes vs modules
    
* Lambdas vs procs vs blocks
    
* Memoization:
    
    * [Beware booleans](https://dev.to/codeandclay/a-memoization-gotcha-14c6)
        
* Logical operators:
    
    * `and` vs `&&`
        
    * `or` vs `||`
        
    * `not` vs `!`
        
* Namespaces
    
* Inheritance
    
* Constants lookup
    
* `super`
    
* `self`
    
* Attribute macros:
    
    * `attr_reader` vs `attr_writer` vs `attr_accessor`
        
* Variables:
    
    * local vs global vs instance variables vs class instance variables vs class variables
        
* Methods visibility:
    
    * **public** vs `protected` vs `private`
        
* Regular expressions:
    
    * [Rubular Playground](https://rubular.com/)
        
* Documentation:
    
    * [YARD](https://rubydoc.info/gems/yard/file/docs/GettingStarted.md)
        

### Frameworks

* [Ruby on Rails](https://rubyonrails.org/):
    
    * [Documentation](https://rubyonrails.org/docs)
        
    * [Guides](https://guides.rubyonrails.org/)
        
    * [The Rails Doctrine](https://rubyonrails.org/doctrine)
        
* [Hanami](https://hanamirb.org/):
    
    * [Documentation](https://docs.hanamirb.org)
        
    * [Guides](https://guides.hanamirb.org/)
        
* [Sinatra](https://sinatrarb.com/):
    
    * [Documentation](https://sinatrarb.com/documentation.html)
        
* [Padrino](https://padrinorb.com/):
    
    * [Documentation](https://www.rubydoc.info/github/padrino/padrino-framework)
        
    * [Guides](https://padrinorb.com/guides/)
        

### Testing

* Frameworks:
    
    * [Minitest](https://github.com/minitest/minitest) (default):
        
        * [Documentation](https://docs.seattlerb.org/minitest/)
            
        * [Learn how to write tests with Minitest](https://github.com/thoughtbot/rails-training-testing-exercise/)
            
    * [RSpec](https://rspec.info/):
        
        * [Documentation](https://rspec.info/documentation/)
            
        * [Blog](https://rspec.info/blog/)
            
    * [Cucumber](https://cucumber.io/):
        
        * [Documentation](https://cucumber.io/docs/)
            
        * [Learn BDD and Cucumber](https://cucumber.io/learn/)
            
        * [Blog](https://cucumber.io/blog/)
            
* Articles:
    
    * [Testing Ruby for beginners](https://thoughtbot.com/blog/how-to-train-your-senior-developers-in-ruby-on-rails#testing-ruby-for-beginners)
        
    * [The Case for WET Tests](https://thoughtbot.com/blog/the-case-for-wet-tests)
        
    * [Mystery Guest](https://thoughtbot.com/blog/mystery-guest)
        
    * [Testing Objects with a Functional Mindset](https://thoughtbot.com/blog/functional-viewpoints-on-testing-objectoriented-code)
        
    * [Four-Phase Test](https://thoughtbot.com/blog/four-phase-test)
        
    * [Write Reliable, Asynchronous Integration Tests With Capybara](https://thoughtbot.com/blog/write-reliable-asynchronous-integration-tests-with-capybara)
        
    * [Thoughtbot](https://thoughtbot.com/blog/tags/testing) (lots of articles)
        

### Interpreters

* [Matz's Ruby Interpreter a.k.a. Ruby MRI a.k.a. CRuby](https://github.com/ruby/ruby)
    
* [JRuby](https://github.com/jruby/jruby)
    
* [Others](https://github.com/codicoscepticos/ruby-implementations)
    

### Installation

* In a real project, whenever possible, it is recommended to use Docker containers.
    
* Version managers:
    
    * [RVM](https://rvm.io/rvm/install#any-other-system)
        
        * `.ruby-version` (include in the version control)
            
        * `.ruby-gemset` (do not include in the version control: Add to `.gitignore`)
            
    * [asdf](https://asdf-vm.com/guide/getting-started.html#_3-install-asdf)
        
    * [rbenv](https://rbenv.org/)
        
    * [chruby](https://github.com/postmodern/chruby)
        
* [GoRails setup](https://gorails.com/setup/)
    

### Editors and IDEs

You will find plugins for Ruby in any editor/IDE.

* [RubyMine](https://www.jetbrains.com/ruby/): the best integration
    
* [VSCode](https://code.visualstudio.com/docs/languages/ruby)
    
    * Add a language server protocol (LSP):
        
        * [Ruby LSP](https://shopify.github.io/ruby-lsp/)
            
        * [Extension](https://marketplace.visualstudio.com/items?itemName=Shopify.ruby-lsp)
            

### Linters

* [RuboCop](https://rubocop.org/)
    
    * [How to start using a linter in a legacy project](https://blog.davidmontesdeoca.es/the-one-about-linting-in-a-legacy-ruby-project)
        
* [Standard](https://github.com/standardrb/standard)
    
* [Brakeman](https://brakemanscanner.org/)
    
* [Reek](https://github.com/troessner/reek)
    

### Style guides

* [Ruby](https://github.com/rubocop/ruby-style-guide)
    
* [Rails](https://github.com/rubocop/rails-style-guide)
    
* [RSpec](https://github.com/rubocop/rspec-style-guide)
    
* [Ruby in Style](https://ruby.style/)
    

### Playgrounds

* [Try Ruby](https://try.ruby-lang.org/)
    
* [Ruby Playground](https://ruby.github.io/play-ruby/)
    
* [Ruby Next Playground](https://ruby-next.github.io/)
    

### Courses

The company provide us with access to the courses catalog available on Udemy.

Some interesting courses to start learning Ruby and Rails:

* [Learn to Code with Ruby](https://www.udemy.com/course/learn-to-code-with-ruby-lang/)
    
* [Testing Ruby with RSpec](https://www.udemy.com/course/testing-ruby-with-rspec/)
    
* [The Complete Ruby on Rails Developer Course](https://www.udemy.com/course/the-complete-ruby-on-rails-developer-course/)
    
* [How To Build Instagram Clone Using Ruby on Rails 7](https://www.udemy.com/course/building-instagram-from-scratch-using-ruby-on-rails-7/)
    

Not yet available for us, but hopefully will be soon:

* [Ruby on Types: Static Typing with Ruby and Ruby on Rails](https://www.udemy.com/course/ruby-on-types-write-robust-software-with-rbs)
    

Other courses:

* [RubyCademy](https://www.rubycademy.com/)
    
* [The Odin Project: Full Stack Ruby on Rails](https://www.theodinproject.com/paths/full-stack-ruby-on-rails):
    
    * [Ruby](https://www.theodinproject.com/paths/full-stack-ruby-on-rails/courses/ruby)
        
    * [Rails](https://www.theodinproject.com/paths/full-stack-ruby-on-rails/courses/ruby-on-rails)
        

### Videos

* [Ruby conferences](https://www.rubyvideo.dev/)
    
* [Upcase](https://thoughtbot.com/upcase/rails)
    
* [Drifting Ruby](https://www.driftingruby.com/)
    
* [GoRails](https://gorails.com/episodes)
    
* [Railscasts](http://railscasts.com/) (outdated)
    

### Books

* [The Pragmatic Bookshelf](https://pragprog.com/search/?q=ruby): lots of books could be outdated
    
    * [Programming Ruby 3.3](https://pragprog.com/titles/ruby5/programming-ruby-3-3-5th-edition/)
        
    * [Agile Web Development with Rails 7](https://pragprog.com/titles/rails7/agile-web-development-with-rails-7/)
        
* [Practical Object-Oriented Design: An Agile Primer Using Ruby (POODR)](https://www.amazon.com/gp/product/0134456475)
    
* [99 Bottles of OOP](https://sandimetz.com/99bottles)
    
* [Ruby Science](https://books.thoughtbot.com/books/ruby-science.html)
    
* [Painless Rails](https://painlessrails.com/)
    
* [The Complete Guide to Rails Performance](https://www.railsspeed.com/)
    
* [Testing Rails](https://books.thoughtbot.com/books/testing-rails.html)
    

### Newsletters

* [Ruby Weekly](https://rubyweekly.com/)
    
* [Short Ruby](https://newsletter.shortruby.com/)
    
* [Ruby Central](https://rubycentral.org/news/)
    

### Exercises and katas

* [Exercism](https://exercism.org/tracks/ruby)
    
* [Codewars](https://www.codewars.com/kata)
    
* [Leetcode](https://leetcode.com/problemset/)
    

### Interesting websites and articles

* [PHP to Ruby](https://phptoruby.io/)
    
* [RubyGuides](https://www.rubyguides.com/ruby-post-index/)
    
* [Design Patterns](https://refactoring.guru/design-patterns): every pattern includes examples in Ruby
    
* [GoRails guides](https://gorails.com/guides)
    
* [Rails hurts](https://railshurts.com/)
    
* [Upgrow: A Better Architecture](https://github.com/backpackerhh/upgrow-docs):
    
    * Proposed by Shopify and somehow inspired by [DDD](https://blog.davidmontesdeoca.es/the-one-with-highlights-of-the-red-book-of-ddd)
        
    * Bear in mind that this is not a usual approach in a Rails project
        
* [How to train senior developers in Ruby on Rails](https://thoughtbot.com/blog/how-to-train-your-senior-developers-in-ruby-on-rails)
    
* [GitLab: Why we're sticking with Ruby on Rails](https://about.gitlab.com/blog/2022/07/06/why-were-sticking-with-ruby-on-rails/)
    
* [Writing Less Error-Prone Code](https://thoughtbot.com/blog/writing-less-error-prone-code)
    

### Useful and popular gems

* [RubyGems](https://rubygems.org/): Find, install, and publish Ruby gems
    
* [Awesome Ruby](https://ruby.libhunt.com/): A collection of awesome Ruby gems, tools, frameworks and software
    
* [The Ruby Toolbox](https://www.ruby-toolbox.com/): Find actively maintained & popular open source software libraries for the Ruby programming language
    

Some gems are available for any Ruby project and others are only available for Rails:

* Testing:
    
    * [RSpec](https://github.com/rspec/rspec)
        
    * [Minitest](https://github.com/minitest/minitest)
        
    * [Cucumber](https://github.com/cucumber/cucumber-ruby)
        
    * [FactoryBot](https://github.com/thoughtbot/factory_bot)
        
    * [Capybara](https://github.com/teamcapybara/capybara)
        
    * [VCR](https://github.com/vcr/vcr)
        
    * [RSwag](https://github.com/rswag/rswag)/[rspec-swag](https://github.com/graceful-potato/rspec-swag)
        
    * [Webmock](https://github.com/bblimke/webmock)
        
    * [Faker](https://github.com/faker-ruby/faker)
        
    * [database-cleaner](https://github.com/DatabaseCleaner/database_cleaner)
        
* Next generation:
    
    * [dry](https://github.com/dry-rb) (dependency injection, types, schemas, validations, monads, ...)
        
* Web servers:
    
    * [Rack](https://github.com/rack/rack) (Interface)
        
* Application servers:
    
    * [puma](https://github.com/puma/puma)
        
    * [passenger](https://github.com/phusion/passenger)
        
* CORS:
    
    * [rack-cors](https://github.com/cyu/rack-cors)
        
* Databases:
    
    * [PostgreSQL](https://github.com/ged/ruby-pg)
        
    * [MySQL](https://github.com/brianmario/mysql2)
        
    * [MongoDB](https://github.com/mongodb/mongo-ruby-driver)
        
    * [Redis](https://github.com/redis/redis-rb)
        
* Migrations:
    
    * [Strong migrations](https://github.com/ankane/strong_migrations)
        
* Bulk imports:
    
    * [ActiveRecord Import](https://github.com/zdennis/activerecord-import)
        
* Cloud:
    
    * [AWS](https://github.com/aws/aws-sdk-ruby)
        
    * [GCP](https://github.com/googleapis/google-cloud-ruby)
        
    * [Azure](https://github.com/Azure/azure-storage-ruby) (no longer maintained)
        
* Background jobs:
    
    * [Sidekiq](https://github.com/sidekiq/sidekiq)
        
    * [Resque](https://github.com/resque/resque)
        
    * [Delayed Jobs](https://github.com/collectiveidea/delayed_job)
        
* Events:
    
    * [Bunny](https://github.com/ruby-amqp/bunny) (RabbitMQ)
        
    * [Kicks](https://github.com/ruby-amqp/kicks) (RabbitMQ)
        
    * [Karafka](https://github.com/karafka/karafka): (Kafka)
        
* Cron:
    
    * [whenever](https://github.com/javan/whenever)
        
    * [rufus-scheduler](https://github.com/jmettraux/rufus-scheduler)
        
    * [Sidekiq Cron](https://github.com/sidekiq-cron/sidekiq-cron)
        
    * [sidekiq-scheduler](https://github.com/sidekiq-scheduler/sidekiq-scheduler)
        
    * [resque-scheduler](https://github.com/resque/resque-scheduler)
        
* WebSocket:
    
    * [AnyCable](https://github.com/anycable/anycable)
        
* Uploads:
    
    * [Shrine](https://github.com/shrinerb/shrine)
        
    * [Carrierwave](https://github.com/carrierwaveuploader/carrierwave)
        
* Caching:
    
    * [Dalli](https://github.com/petergoldstein/dalli) (Memcached)
        
* Full text search:
    
    * [Elasticsearch](https://github.com/elastic/elasticsearch-ruby)
        
    * [OpenSearch](https://github.com/opensearch-project/opensearch-ruby)
        
    * [Algolia](https://github.com/algolia/algoliasearch-client-ruby)
        
* HTTP client:
    
    * [Faraday](https://github.com/lostisland/faraday)
        
    * [httparty](https://github.com/jnunemaker/httparty)
        
* APIs:
    
    * [GraphQL](https://github.com/rmosolgo/graphql-ruby)
        
    * [JSON:API serializer](https://github.com/jsonapi-serializer/jsonapi-serializer)
        
    * [ActiveModel serializers](https://github.com/rails-api/active_model_serializers)
        
    * [JWT](https://github.com/jwt/ruby-jwt)
        
* Deployment:
    
    * [Capistrano](https://github.com/capistrano/capistrano)
        
* Geocoding:
    
    * [Geocoder](https://github.com/alexreisner/geocoder)
        
* Feature flags:
    
    * [Flipper](https://github.com/flippercloud/flipper)
        
* Authentication:
    
    * [Devise](https://github.com/heartcombo/devise)
        
    * [Doorkeeper](https://github.com/doorkeeper-gem/doorkeeper)
        
    * [Omniauth](https://github.com/omniauth/omniauth): Standardized Multi-Provider Authentication
        
    * [Clearance](https://github.com/thoughtbot/clearance)
        
* Authorization:
    
    * [Pundit](https://github.com/varvet/pundit)
        
    * [CanCanCan](https://github.com/cancancommunity/cancancan)
        
    * [ActionPolicy](https://github.com/palkan/action_policy)
        
* Mobile:
    
    * Hotwire Native: [Android](https://github.com/hotwired/hotwire-native-android)/[iOS](https://github.com/hotwired/hotwire-native-ios)
        
* Templates:
    
    * [ERB](https://github.com/ruby/erb)
        
    * [Haml](https://github.com/haml/haml)
        
    * [Slim](https://github.com/slim-template/slim)
        
* Assets:
    
    * [Sass](https://github.com/sass-contrib/sass-embedded-host-ruby)
        
    * [Tailwind](https://github.com/rails/tailwindcss-rails)
        
    * [Bootstrap](https://github.com/twbs/bootstrap-rubygem)
        
    * [React](https://github.com/reactjs/react-rails)
        
    * [htmx](https://github.com/bkuhlmann/htmx)
        
* Interactivity:
    
    * [Stimulus](https://github.com/hotwired/stimulus-rails)
        
    * [Turbo](https://github.com/hotwired/turbo-rails)
        
* Views:
    
    * [View Component](https://viewcomponent.org/)
        
* Forms:
    
    * [Simple Form](https://github.com/heartcombo/simple_form)
        
* Pagination:
    
    * [Kaminari](https://github.com/kaminari/kaminari)
        
    * [will\_paginate](https://github.com/mislav/will_paginate)
        
    * [pagy](https://github.com/ddnexus/pagy)
        
* State machines:
    
    * [State Machines](https://github.com/state-machines/state_machines)
        
    * [AASM](https://github.com/aasm/aasm)
        
* Videos:
    
    * [m3u8](https://github.com/sethdeckard/m3u8)
        
* Money:
    
    * [money](https://github.com/RubyMoney/money)
        
* Audit changes:
    
    * [audited](https://github.com/collectiveidea/audited)
        
* Dependency updates:
    
    * [Renovate](https://github.com/renovatebot/renovate)
        

### Community

Last but not least, get involved in the community the way you prefer. Follow people in social networks, read as much as you can about the language, its frameworks and tools, and even collaborate in open source projects if you feel like it.

In your city probably there is a Ruby group that gathers from time to time, usually monthly, to talk about the language and related stuff. For instance, in Madrid, where I am currently living, we have [Madrid.rb](https://www.madridrb.com/).

---

That training for me was an enriching experience that took about 4 weeks. My colleagues approached the training as a challenge and at all times I felt that they were really interested in learning the language and its ecosystem, and that they wanted to put into practice what they were learning.

My boss told me that they were very happy with the training and I can confirm the guide I created will be used for future trainings at SNGULAR.

I would also like to take this opportunity to thank my mentor, [Eddy Josafat](https://www.linkedin.com/in/eddyjosafat), for coming up with the brilliant idea of using this language in which he saw a promising future. He was not wrong.

By the way, this is a list that I will keep updating over time. Is there anything you miss in this list? If so, leave it in the comments, please.

Thank you for reading and see you in the next one!