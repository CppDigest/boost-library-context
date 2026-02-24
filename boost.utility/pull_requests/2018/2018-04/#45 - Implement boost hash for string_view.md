# #45 Implement boost hash for string_view [Merged]

> Username: dimztimz  
> Created at: 2018-04-20 11:43:47 UTC  
> Updated at: 2018-07-11 18:51:05 UTC  
> Merged at: 2018-05-01 14:51:37 UTC  
> Closed at: 2018-05-01 14:51:37 UTC  
> Url: https://github.com/boostorg/utility/pull/45  

Fixes #44

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2018-04-20 13:56:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/utility/pull/45#pullrequestreview-113987418  

📁 include/boost/utility/string_view.hpp

```diff
  22 | #include <boost/utility/string_view_fwd.hpp>
  23 | #include <boost/throw_exception.hpp>
  24 |+ #include <boost/container_hash/hash.hpp>
```

> Username: Lastique  
> Created_at: 2018-04-20 13:56:26 UTC  
> Updated_at: 2018-04-20 21:18:46 UTC  
> Url: https://github.com/boostorg/utility/pull/45#discussion_r183058619  

`hash_fwd.hpp` should be enough here.

> Username: dimztimz  
> Created_at: 2018-04-20 21:21:14 UTC  
> Url: https://github.com/boostorg/utility/pull/45#discussion_r183172378  

Ok makes sense. `hash.hpp` will end up included anyway by unordered or multi_index.


---

## Comment 2

> Username: dimztimz  
> Created_at: 2018-04-20 21:23:59 UTC  
> Url: https://github.com/boostorg/utility/pull/45#issuecomment-383226640  

Fixed the hash_fwd thingy. Will require `hash.hpp` to be included if we are going to call the hash function manually, but that is usually not needed. The hash containers that call this func already bring in that header.

---

## Comment 3

> Username: dimztimz  
> Created_at: 2018-04-30 10:10:12 UTC  
> Url: https://github.com/boostorg/utility/pull/45#issuecomment-385358437  

Please merge this.

---

## Comment 4

> Username: mclow  
> Created_at: 2018-05-01 14:51:55 UTC  
> Url: https://github.com/boostorg/utility/pull/45#issuecomment-385690134  

Please remind me to merge this to master after the tests have cycled.

---

## Comment 5

> Username: dimztimz  
> Created_at: 2018-07-11 18:51:04 UTC  
> Url: https://github.com/boostorg/utility/pull/45#issuecomment-404273397  

This is still not merged into master. Travis CI tests seem to pass.

---
