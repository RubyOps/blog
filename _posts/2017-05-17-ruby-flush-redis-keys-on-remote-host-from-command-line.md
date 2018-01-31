---
layout: post
title: Ruby: flush all redis keys on a remote host from command line
tags: [Ruby, Redis]
---

Install `redis` gem.

```
gem install redis
```
Execute from Linux shell.

{% highlight ruby %}
ruby -e 'require "redis"
redis = Redis.new(:host => "remote.host.ip.address", :password => "PASSWORD", :port => 6379)
redis.flushall'
{% endhighlight %}

Simple as that.
