# #5 Use pointer_traits.to_address instead of addressof(*p) [Closed]

> Username: glenfe  
> Created at: 2017-05-29 18:26:30 UTC  
> Updated at: 2017-06-08 16:24:05 UTC  
> Closed at: 2017-06-08 16:24:05 UTC  
> Url: https://github.com/boostorg/unordered/pull/5  

`addressof(*p)` before calling `construct()` is technically undefined as there is no object yet constructed in the storage aliased by `p` (given that `addressof()` takes an l-value reference).

---

## Comment 1

> Username: danieljames  
> Created_at: 2017-05-30 09:42:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/5#issuecomment-304827050  

There are a lot of other places where that would apply. I'll look into it, but I might just handle it directly.

---

## Comment 2

> Username: glenfe  
> Created_at: 2017-05-30 16:33:36 UTC  
> Url: https://github.com/boostorg/unordered/pull/5#issuecomment-304934901  

Sounds good. I merged pointer_traits to master; you might want to also have Travis pull either develop or master  versions of repositories instead (or in addition to) the latest Boost release archive.

---
