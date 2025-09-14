---
title: "Ruby Blocks and Iterators: The Heart of Ruby Programming"
date: 2025-09-10 14:30:00 -0800
categories: [Programming, Ruby]
tags: [ruby, blocks, iterators, programming]
author: dain
---

Ruby's blocks and iterators are one of its most powerful and distinctive features. They provide an elegant way to work with collections and implement higher-order functions.

## What are Blocks?

Blocks in Ruby are chunks of code that can be passed to methods. They're like anonymous functions that can be executed within the context of a method.

```ruby
# Block with do...end
[1, 2, 3, 4, 5].each do |number|
  puts number * 2
end

# Block with curly braces
[1, 2, 3, 4, 5].each { |number| puts number * 2 }
```

## Common Iterators

### each
The most basic iterator that executes a block for each element:

```ruby
fruits = ['apple', 'banana', 'cherry']
fruits.each { |fruit| puts "I love #{fruit}!" }
```

### map/collect
Transforms each element and returns a new array:

```ruby
numbers = [1, 2, 3, 4, 5]
squared = numbers.map { |n| n ** 2 }
# => [1, 4, 9, 16, 25]
```

### select/filter
Returns elements that match a condition:

```ruby
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
evens = numbers.select { |n| n.even? }
# => [2, 4, 6, 8, 10]
```

### reject
Returns elements that don't match a condition:

```ruby
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
odds = numbers.reject { |n| n.even? }
# => [1, 3, 5, 7, 9]
```

## Creating Custom Iterators

You can create your own methods that accept blocks:

```ruby
def repeat(times)
  counter = 0
  while counter < times
    yield(counter)
    counter += 1
  end
end

repeat(3) { |i| puts "Iteration #{i}" }
```

## Proc and Lambda

For more advanced use cases, Ruby provides `Proc` and `lambda`:

```ruby
# Proc
my_proc = Proc.new { |x| x * 2 }
puts my_proc.call(5) # => 10

# Lambda
my_lambda = lambda { |x| x * 2 }
puts my_lambda.call(5) # => 10

# Lambda with stabby syntax
my_lambda = ->(x) { x * 2 }
puts my_lambda.call(5) # => 10
```

Blocks and iterators make Ruby code more expressive and functional. They're essential tools for any Ruby developer!
