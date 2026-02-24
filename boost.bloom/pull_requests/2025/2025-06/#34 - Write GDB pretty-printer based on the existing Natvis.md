# #34 Write GDB pretty-printer based on the existing Natvis [Merged]

> Username: k3DW  
> Created at: 2025-06-25 10:33:41 UTC  
> Updated at: 2025-06-26 19:04:26 UTC  
> Merged at: 2025-06-26 19:04:03 UTC  
> Closed at: 2025-06-26 19:04:03 UTC  
> Url: https://github.com/boostorg/bloom/pull/34  

Addresses #15

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-06-25 11:03:20 UTC  
> Url: https://github.com/boostorg/bloom/pull/34#issuecomment-3004364600  

An automated preview of the documentation is available at [https://34.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://34.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)

---

## Review 2 [Commented]

> Username: joaquintides  
> Created_at: 2025-06-25 14:11:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/bloom/pull/34#pullrequestreview-2958342570  

📁 extra/boost_bloom_printers.py

```diff
  28 |+ 
  29 |+     def display_hint(self):
  30 |+         return "map"
```

> Username: joaquintides  
> Created_at: 2025-06-25 14:11:48 UTC  
> Updated_at: 2025-06-25 14:11:49 UTC  
> Url: https://github.com/boostorg/bloom/pull/34#discussion_r2166826309  

Whats is this function for? And shouldn't it return `"filter"`instead?

> Username: k3DW  
> Created_at: 2025-06-25 14:41:10 UTC  
> Url: https://github.com/boostorg/bloom/pull/34#discussion_r2166905785  

This function tells GDB how to interpret the `children()` function. It's unrelated to the semantic meaning of the contents


---

## Review 3 [Commented]

> Username: joaquintides  
> Created_at: 2025-06-25 14:13:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/bloom/pull/34#pullrequestreview-2958350774  

I have some comments:  
  
* `<boost/bloom/bloom_printers.hpp>` is included in `<boost/bloom.hpp>`, but I think it should be included in `<boost/bloom/filter.hpp>` (it is possible to use `boost::bloom::filter` without including `<boost/bloom.hpp>`).  
* I know we're following the same scheme as in Boost.Unordered, but I wonder why `<boost/bloom/bloom_printers.hpp>` needs be a "public" header instead of living in `detail`.

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2025-06-25 15:03:11 UTC  
> Url: https://github.com/boostorg/bloom/pull/34#issuecomment-3005124742  

An automated preview of the documentation is available at [https://34.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://34.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)

---
