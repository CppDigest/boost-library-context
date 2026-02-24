# #110 Switch to `boost::core::invoke_swap` [Merged]

> Username: Lastique  
> Created at: 2023-09-02 23:22:17 UTC  
> Updated at: 2023-10-01 13:03:33 UTC  
> Merged at: 2023-10-01 09:36:37 UTC  
> Closed at: 2023-10-01 09:36:37 UTC  
> Url: https://github.com/boostorg/optional/pull/110  

`boost::swap` is deprecated and will be removed. Use `boost::core::invoke_swap` as a replacement.

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-09-30 10:58:58 UTC  
> Url: https://github.com/boostorg/optional/pull/110#issuecomment-1741740473  

Can we please have either this or #112 merged? The deprecation warnings are annoying, and `optional.hpp` is often being included indirectly.

---
