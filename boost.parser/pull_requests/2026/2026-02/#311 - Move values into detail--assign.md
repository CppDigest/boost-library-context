# #311 Move values into detail::assign [Merged]

> Username: andreasbuhr  
> Created at: 2026-02-03 10:26:25 UTC  
> Updated at: 2026-02-14 19:42:10 UTC  
> Merged at: 2026-02-14 19:42:09 UTC  
> Closed at: 2026-02-14 19:42:09 UTC  
> Url: https://github.com/boostorg/parser/pull/311  

Code cleanup:  
Most of the time, values are std::moved into detail::assign, but not always. This patch makes usage more consistent to always use std::move, except for ints, floats, and iterators.

---

## Comment 1

> Username: tzlaine  
> Created_at: 2026-02-14 19:42:06 UTC  
> Url: https://github.com/boostorg/parser/pull/311#issuecomment-3902404594  

Nice catch.

---
