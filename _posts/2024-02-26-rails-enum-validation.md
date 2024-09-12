---
title: "Validate option for enums introduced on Rails ^7.1"
layout: post
date: 2024-02-26 16:48
image: /assets/images/markdown.jpg
headerImage: false
tag:
- rails
- enum
- validations
star: true
category: blog
author: andreaflether
description: In Rails 7.1, a new :validate option for enums was introduced, making it easier to ensure that the values assigned to an enum attribute are valid before saving.

---

### What are enums?

A Rails enum is a way to define a set of allowed values for an attribute in a Rails model. Enums are stored as integers in the database but can be accessed and used in Ruby as symbols.

To define an enum, you use the enum method in your model. The enum method takes two arguments: the name of the attribute and a list of values. The values can be either an array or a hash.

Enums were added to Rails in the 4.1 version, but until Rails 7.1, the ability to validate the enums was missing.

### Before
Suppose you have a Rails application with a `Course` model that includes a `status` column. 
This column is an enum that indicates whether the course is pending, active or archived.

We can illustrate this by the following example:

```ruby
class Course < ActiveRecord::Base
  enum status: %i[pending active archived]
  validates :status, inclusion: { in: %i[pending active archived] }
end
```

If you tried to assign a value like `disabled`, an `ArgumentError` would be thrown right away:

```ruby
course = Course.last
course.status = :disabled 
#=> 'disabled' is not a valid status (ArgumentError)
```

This was complicated because if you really wanted to validate an enum value, you had to use other gems or add hacky codes like:

```ruby
class Course < ActiveRecord::Base
  enum status: %i[pending active archived]

  validates_inclusion_of :status, in: statuses.keys

  def status=(value)
    super
  rescue ArgumentError
    @attributes.write_cast_value("status", value)
  end
end
```

This was a problem in Rails for a very long time, as detailed in this [PR](https://github.com/rails/rails/pull/13971). 

### After

Now in Rails versions higher than 7.1, it's possible to validate enums in a very easy and native way. You can do this by simply adding the `validate: true` option to the enum definition itself:

```ruby
class Course < ApplicationRecord
  enum status: %i[pending active archived], validate: true
end
```

Then, when you try to assign an invalid value, it will no longer throw an `ArgumentError`, but instead an `false` from the `valid?` call:

```ruby
course = Course.find(1)

course.status = :disabled
course.valid? #=> false

course.status = :active
course.valid? #=> true
```

You can also pass additional validation options, if the field itself is not required:

```ruby
class Course < ApplicationRecord
  enum status: %i[pending active archived], validate: { allow_nil: true }
end

course = Course.last

course.status = :disabled
course.valid? #=> false

course.status = nil
course.valid? #=> true
```

If you don't pass the `validate` option, it will raise `ArgumentError` as in the earlier versions.

More details about this feature can be found in [PR #49100](https://github.com/rails/rails/pull/49100).