# BluntCache
Simple in-memory cache service for Ruby.

[![Build Status](https://travis-ci.org/appelsin/blunt_cache.svg?branch=master)](https://travis-ci.org/appelsin/blunt_cache)
[![Code Climate](https://codeclimate.com/github/appelsin/blunt_cache/badges/gpa.svg)](https://codeclimate.com/github/appelsin/blunt_cache)

## Usage

```ruby
# set/get by key
BluntCache.set "key", data
data = BluntCache.get "key"

# executes block if not set or expired
data = BluntCache.fetch "key" do
  do_something
end

# time to live can be provided (default is 60 sec)
BluntCache.set "key", data, expire: 120
BluntCache.fetch "key", expire: 120 do
  do_something
end

# checks if key exists
BluntCache.key? "nil_key" # false
BluntCache.set "nil_key", nil
BluntCache.key? "nil_key" # true

# doesn't re-executes nil fetches
data = BluntCache.fetch("heavy_key") { sleep 1; nil } # some heavy block returning nil
data = BluntCache.fetch("heavy_key") { sleep 1; nil } # doesn't re-executes if not expired

# inherit it for namespacing and extending
class MyCache < BluntCache
  @expire_default = 30
end

MyCache.set "1", "2"
BluntCache.set "1", "3"
MyCache.get "1" #2
```

## Why? When to use?

* It is fast.
* Use it when you don't want to execute serialization-deserialization cycle with real cache (Redis or Memcache).
* Use it when you not able (or don't want) to use external cache service (you or your admin are lazy, when using Heroku or other cloud services, etc).

## Limitations

* Keep in mind that values are stored in memory even if they are expired. As for replaced values, let's just belive in Ruby GC abilities. So, Ruby workers can bloat.
* Keep in mind that Cache IS NOT shared between workers (e.g. Unicorn, Puma cluster workers) and IS shared between threads (Puma).
