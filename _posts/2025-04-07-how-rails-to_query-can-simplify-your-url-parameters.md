---
title: "How Rails’ .to_query can simplify your URL parameters"
layout: post
date: 2025-04-07 20:45
headerImage: false
tag:
- rails
- to_query
- active-support
- query-strings
star: false
category: blog
author: andreaflether
description: Learn how the .to_query method simplifies building query strings with real-world examples and structured parameter wrapping.
---

# How Rails’ .to_query Can Simplify Your URL Parameters

When working with web applications, you often need to convert data structures into URL query strings. This is where Rails' `.to_query` method shines! It's a handy method that turns a hash (or other object types) into a properly escaped query string you can use in URLs. 

In this post, we’ll break down what `.to_query` is, how it works, and provide some easy-to-follow examples.

## What is `.to_query`?

`.to_query` is a method available in Rails (part of ActiveSupport) that converts an object—most commonly a Hash or an Array—into a URL-encoded query string.

It’s especially useful when you need to:

- Build query strings manually  
- Pass parameters in a URL  
- Work with external APIs

## Basic Usage

**Let’s start with a simple example:**

```ruby
params = { name: "Samantha", age: 35 }
params.to_query
# => "name=Samantha&age=35"
```

That’s it! Rails automatically handles the encoding and formatting.

## Passing to URLs
You can easily use `.to_query` when building a link:

```ruby
base_url = "https://example.com/search"
query = { q: "Rails", page: 2 }.to_query
full_url = "#{base_url}?#{query}"
# => "https://example.com/search?q=Rails&page=2"
```

You can also use `.to_query` to wrap your entire parameter set inside a single key, which is especially useful when working with APIs that expect parameters grouped under a specific namespace.

```ruby
params = { title_i_cont: "Test", author_name_i_cont: "Andrea" }
params.to_query('q')
# => "q%5Bauthor_name_i_cont%5D=Andrea&q%5Btitle_i_cont%5D=Test"
```

This is equivalent to:

```
?q[author_name_i_cont]=Andrea&q[title_i_cont]=Test
```

This eliminates the need to do manual string concatenation and it comes in very handy in multiple occasions!

## Nested Hashes
If your hash contains another hash inside it, `.to_query` will nest the parameters:

```ruby
params = { post: { title: "Post Title", status: "published" } }
params.to_query
# => "post%5Btitle%5D=Post+Title&post%5Bstatus%5D=published"
```

Which is the equivalent to:

```
"post[title]=Post Title&post[posted_on]=2025-04-07"
```

This format is useful for sending structured data (like a POST when creating a resource) in requests.

## Arrays in Queries

Rails also supports arrays:

```ruby
params = { tags: ["rails", "ruby"] }
params.to_query
# => "tags[]=rails&tags[]=ruby"
```

This is perfect for filters, multi-selects, or tagging systems.

---

The `.to_query` method in Rails is a small but powerful tool for anyone working with URLs, forms, or APIs. It simplifies the process of turning hashes and arrays into clean, readable, and valid query strings.

Next time you need to build a query string, skip the manual formatting and let `.to_query` handle it for you!
