---
title: "Ruby Metaprogramming: Writing Code that Writes Code"
date: 2025-09-11 09:15:00 -0800
categories: [Programming, Ruby]
tags: [ruby, metaprogramming, dynamic, reflection]
author: dain
---

Metaprogramming is one of Ruby's most powerful features, allowing you to write code that manipulates code itself at runtime. It's what makes Ruby so flexible and dynamic.

## What is Metaprogramming?

Metaprogramming is the practice of writing programs that can read, generate, analyze, or transform other programs, and even modify themselves while running.

## Dynamic Method Definition

You can define methods dynamically using `define_method`:

```ruby
class Person
  %w[name age email].each do |attribute|
    define_method attribute do
      instance_variable_get("@#{attribute}")
    end
    
    define_method "#{attribute}=" do |value|
      instance_variable_set("@#{attribute}", value)
    end
  end
end

person = Person.new
person.name = "John"
person.age = 30
puts person.name # => "John"
```

## Method Missing

The `method_missing` hook allows you to respond to undefined method calls:

```ruby
class MagicHash
  def initialize
    @data = {}
  end
  
  def method_missing(method_name, *args)
    if method_name.to_s.end_with?('=')
      # Setter method
      key = method_name.to_s.chomp('=')
      @data[key] = args.first
    else
      # Getter method
      @data[method_name.to_s]
    end
  end
  
  def respond_to_missing?(method_name, include_private = false)
    true
  end
end

hash = MagicHash.new
hash.username = "johndoe"
hash.email = "john@example.com"
puts hash.username # => "johndoe"
```

## Class Macros

Ruby's `attr_accessor`, `attr_reader`, and `attr_writer` are examples of class macros:

```ruby
class Product
  attr_accessor :name, :price
  attr_reader :id
  
  def initialize(id, name, price)
    @id = id
    @name = name
    @price = price
  end
end
```

You can create your own class macros:

```ruby
class ActiveRecord
  def self.validates_presence_of(*attributes)
    attributes.each do |attribute|
      define_method "validate_#{attribute}" do
        if instance_variable_get("@#{attribute}").nil?
          raise "#{attribute} cannot be nil"
        end
      end
    end
  end
end

class User < ActiveRecord
  validates_presence_of :email, :password
  
  attr_accessor :email, :password
end
```

## Module Inclusion Hooks

Ruby provides hooks that are called when modules are included:

```ruby
module Trackable
  def self.included(base)
    base.extend(ClassMethods)
    base.class_eval do
      attr_accessor :created_at, :updated_at
    end
  end
  
  module ClassMethods
    def track_creation
      define_method :initialize do |*args|
        super(*args)
        @created_at = Time.now
        @updated_at = Time.now
      end
    end
  end
  
  def touch
    @updated_at = Time.now
  end
end

class Article
  include Trackable
  track_creation
  
  attr_accessor :title, :content
end
```

## Eval Family

Ruby provides several `eval` methods for dynamic code execution:

```ruby
# eval - executes a string as Ruby code
code = "puts 'Hello from eval!'"
eval(code)

# instance_eval - executes code in the context of an object
person = Person.new
person.instance_eval do
  @name = "Alice"
  @age = 25
end

# class_eval - executes code in the context of a class
Person.class_eval do
  def greet
    "Hello, I'm #{@name}"
  end
end
```

## Best Practices

1. **Use metaprogramming sparingly** - It can make code hard to understand and debug
2. **Prefer explicit over implicit** - Clear code is better than clever code
3. **Document your metaprogramming** - Future you will thank you
4. **Test thoroughly** - Dynamic code is harder to catch with static analysis

Metaprogramming is a double-edged sword. Use it wisely to create elegant, DRY code, but don't sacrifice readability for cleverness.
