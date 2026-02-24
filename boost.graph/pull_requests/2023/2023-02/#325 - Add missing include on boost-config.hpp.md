# #325 Add missing include on boost/config.hpp [Merged]

> Username: ams2990  
> Created at: 2023-02-10 00:03:30 UTC  
> Updated at: 2023-02-15 04:25:09 UTC  
> Merged at: 2023-02-15 04:25:09 UTC  
> Closed at: 2023-02-15 04:25:09 UTC  
> Url: https://github.com/boostorg/graph/pull/325  

exception.hpp uses BOOST_SYMBOL_VISIBLE, which is defined in boost/config.hpp. Without this, compiling just exception.hpp is broken.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2023-02-15 04:24:54 UTC  
> Url: https://github.com/boostorg/graph/pull/325#issuecomment-1430739293  

Thanks!

---
