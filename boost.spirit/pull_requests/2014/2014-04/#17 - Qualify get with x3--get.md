# #17 Qualify get with x3::get [Merged]

> Username: wichtounet  
> Created at: 2014-04-26 08:58:42 UTC  
> Updated at: 2014-06-14 07:39:02 UTC  
> Merged at: 2014-05-01 20:32:59 UTC  
> Closed at: 2014-05-01 20:32:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/17  

x3::get must be used to get the skipper_tag instead of simply get

---

## Comment 1

> Username: K-ballo  
> Created_at: 2014-04-26 17:00:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/17#issuecomment-41474133  

Why? The calls are already on `x3` namespace, and the explicit template argument disables ADL.

---

## Comment 2

> Username: wichtounet  
> Created_at: 2014-04-26 17:49:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/17#issuecomment-41475524  

Because when used on a context generated from any_parser, it causes boost::get(optional<T>) to be preferred over x3::get.

---

## Comment 3

> Username: K-ballo  
> Created_at: 2014-04-26 18:11:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/17#issuecomment-41476176  

But there is no reason why that should happen, is there? What I'm trying to say is, unless I'm missing something, this is a workaround and not a fix. I would like to know what the actual issue is here, and I'm still investigating your minimal test case since the issue manifests there but not in our tests and the example.

---
