---
title: "Testing Ruby Code with RSpec: A Comprehensive Guide"
date: 2025-09-13 11:20:00 -0800
categories: [Programming, Ruby, Testing]
tags: [ruby, rspec, testing, tdd, bdd]
author: dain
---

RSpec is Ruby's most popular testing framework, providing a domain-specific language for behavior-driven development. Let's explore how to write effective tests with RSpec.

## Getting Started with RSpec

Add RSpec to your Gemfile:

```ruby
group :development, :test do
  gem 'rspec-rails'  # For Rails apps
  # or
  gem 'rspec'        # For non-Rails apps
end
```

Initialize RSpec:

```bash
bundle install
rspec --init
```

This creates a `spec` directory and `spec_helper.rb` file.

## Basic RSpec Structure

RSpec tests are organized with `describe` and `it` blocks:

```ruby
# spec/calculator_spec.rb
require 'spec_helper'
require_relative '../lib/calculator'

RSpec.describe Calculator do
  describe '#add' do
    it 'returns the sum of two numbers' do
      calculator = Calculator.new
      result = calculator.add(2, 3)
      expect(result).to eq(5)
    end
    
    it 'handles negative numbers' do
      calculator = Calculator.new
      result = calculator.add(-2, 3)
      expect(result).to eq(1)
    end
  end
  
  describe '#divide' do
    it 'returns the quotient of two numbers' do
      calculator = Calculator.new
      result = calculator.divide(10, 2)
      expect(result).to eq(5)
    end
    
    it 'raises an error when dividing by zero' do
      calculator = Calculator.new
      expect { calculator.divide(10, 0) }.to raise_error(ZeroDivisionError)
    end
  end
end
```

## RSpec Matchers

RSpec provides many built-in matchers:

```ruby
# Equality
expect(actual).to eq(expected)
expect(actual).to eql(expected)
expect(actual).to be(expected)

# Comparison
expect(actual).to be > 3
expect(actual).to be_between(1, 10)

# Type checking
expect(actual).to be_a(String)
expect(actual).to be_an(Integer)
expect(actual).to be_instance_of(String)

# Truthiness
expect(actual).to be_truthy
expect(actual).to be_falsy
expect(actual).to be_nil

# Collections
expect(actual).to include('item')
expect(actual).to contain_exactly(1, 2, 3)
expect(actual).to match_array([3, 1, 2])

# Strings
expect(actual).to start_with('Hello')
expect(actual).to end_with('world')
expect(actual).to match(/regex/)

# Exceptions
expect { some_code }.to raise_error(StandardError)
expect { some_code }.to raise_error(StandardError, 'message')
expect { some_code }.not_to raise_error
```

## Before and After Hooks

Set up and tear down test data:

```ruby
RSpec.describe User do
  before(:each) do
    @user = User.new(name: 'John', email: 'john@example.com')
  end
  
  after(:each) do
    # Cleanup code
  end
  
  before(:all) do
    # Run once before all tests in this describe block
  end
  
  after(:all) do
    # Run once after all tests in this describe block
  end
  
  it 'has a valid name' do
    expect(@user.name).to eq('John')
  end
end
```

## Subject and Let

Use `let` for lazy-loaded variables and `subject` for the main object under test:

```ruby
RSpec.describe User do
  let(:user) { User.new(name: 'John', email: 'john@example.com') }
  let!(:admin) { User.create(name: 'Admin', admin: true) } # let! is eager-loaded
  
  subject { user }
  
  it 'has a name' do
    expect(subject.name).to eq('John')
  end
  
  it { is_expected.to be_valid }
  it { is_expected.not_to be_admin }
end
```

## Shared Examples

Reuse test logic across multiple specs:

```ruby
# spec/support/shared_examples.rb
RSpec.shared_examples 'a valid email' do
  it 'accepts valid email formats' do
    valid_emails = ['test@example.com', 'user.name@domain.co.uk']
    valid_emails.each do |email|
      subject.email = email
      expect(subject).to be_valid
    end
  end
  
  it 'rejects invalid email formats' do
    invalid_emails = ['invalid', '@example.com', 'test@']
    invalid_emails.each do |email|
      subject.email = email
      expect(subject).not_to be_valid
    end
  end
end

# Usage in specs
RSpec.describe User do
  subject { User.new(name: 'John') }
  
  it_behaves_like 'a valid email'
end
```

## Mocking and Stubbing

RSpec provides built-in mocking capabilities:

```ruby
RSpec.describe EmailService do
  let(:mailer) { double('Mailer') }
  let(:service) { EmailService.new(mailer) }
  
  describe '#send_welcome_email' do
    it 'sends an email to the user' do
      user = User.new(email: 'john@example.com')
      
      expect(mailer).to receive(:send_email)
        .with(user.email, 'Welcome!')
        .and_return(true)
      
      result = service.send_welcome_email(user)
      expect(result).to be true
    end
  end
  
  describe '#user_count' do
    it 'returns the number of users' do
      allow(User).to receive(:count).and_return(42)
      
      expect(service.user_count).to eq(42)
    end
  end
end
```

## Testing Class Methods

```ruby
RSpec.describe User do
  describe '.find_by_email' do
    it 'returns a user with the given email' do
      user = User.create(email: 'john@example.com')
      
      found_user = User.find_by_email('john@example.com')
      expect(found_user).to eq(user)
    end
    
    it 'returns nil if no user found' do
      result = User.find_by_email('nonexistent@example.com')
      expect(result).to be_nil
    end
  end
end
```

## Custom Matchers

Create your own matchers for domain-specific assertions:

```ruby
# spec/support/matchers.rb
RSpec::Matchers.define :be_a_valid_email do
  match do |actual|
    actual =~ /\A[\w+\-.]+@[a-z\d\-]+(\.[a-z\d\-]+)*\.[a-z]+\z/i
  end
  
  failure_message do |actual|
    "expected '#{actual}' to be a valid email address"
  end
end

# Usage
expect('john@example.com').to be_a_valid_email
expect('invalid-email').not_to be_a_valid_email
```

## Running Tests

```bash
# Run all tests
rspec

# Run specific file
rspec spec/models/user_spec.rb

# Run specific test
rspec spec/models/user_spec.rb:25

# Run with specific format
rspec --format documentation
rspec --format json

# Run with tags
rspec --tag focus
rspec --tag ~slow  # Exclude slow tests
```

## Best Practices

1. **Use descriptive test names** that explain the behavior
2. **Follow AAA pattern** (Arrange, Act, Assert)
3. **One assertion per test** when possible
4. **Use `let` for setup** instead of instance variables
5. **Test behavior, not implementation**
6. **Use shared examples** for common behavior
7. **Mock external dependencies**
8. **Keep tests fast** by avoiding unnecessary database hits

RSpec makes testing Ruby code enjoyable and expressive. Good tests give you confidence to refactor and add new features!
