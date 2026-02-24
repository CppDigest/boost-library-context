# #241 [map] use type-level hash [Closed]

> Username: ricejasonf  
> Created at: 2016-01-20 21:18:59 UTC  
> Updated at: 2016-01-21 00:05:46 UTC  
> Closed at: 2016-01-21 00:03:08 UTC  
> Url: https://github.com/boostorg/hana/pull/241  

```  
- Hashable concept  
- hash function  
```

---

## Comment 1

> Username: ricejasonf  
> Created_at: 2016-01-20 21:26:38 UTC  
> Url: https://github.com/boostorg/hana/pull/241#issuecomment-173364011  

As it relates to #118, I'm not sure if I hit the mark. Here, buckets hold exactly one element, `hash` only seems to accomplish converting integral constants to a common type, and `Comparable` is completely ignored.  
  
Also, I may have gone overboard with the `Hashable` concept, but it seems that users should be able to specialize `hash_impl`.  
  
There are some other rough edges that need rounding out, but I wanted to get some feedback on what I have so far.

---

## Comment 2

> Username: ldionne  
> Created_at: 2016-01-21 00:04:08 UTC  
> Url: https://github.com/boostorg/hana/pull/241#issuecomment-173407754  

Wtf? How did I close this?

---

## Comment 3

> Username: ldionne  
> Created_at: 2016-01-21 00:05:46 UTC  
> Url: https://github.com/boostorg/hana/pull/241#issuecomment-173408010  

Ahh! Please re-create this pull request, but targeting the `develop` branch instead!

---
