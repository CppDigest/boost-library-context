# #532 Add BOOST_LIBSTDCXX_VERSION_WORKAROUND_GUARD [Merged]

> Username: pdimov  
> Created at: 2026-01-04 12:20:44 UTC  
> Updated at: 2026-01-04 13:57:57 UTC  
> Merged at: 2026-01-04 13:57:56 UTC  
> Closed at: 2026-01-04 13:57:56 UTC  
> Url: https://github.com/boostorg/config/pull/532  

This avoids the `-Wundef` warning when `BOOST_WORKAROUND(BOOST_LIBSTDCXX_VERSION, ...)` is used without a `defined(BOOST_LIBSTDCXX_VERSION)` check.

---

## Comment 1

> Username: pdimov  
> Created_at: 2026-01-04 13:57:36 UTC  
> Url: https://github.com/boostorg/config/pull/532#issuecomment-3708109458  

Merging because the `-Wundef` warning is unavoidable on the user side even if guarded.  
  
```  
./boost/unordered/detail/type_traits.hpp:66:58: error: "BOOST_LIBSTDCXX_VERSION_WORKAROUND_GUARD" is not defined [-Werror=undef]  
 #if defined(BOOST_LIBSTDCXX_VERSION) && BOOST_WORKAROUND(BOOST_LIBSTDCXX_VERSION, < 50000)  
                                                          ^  
```

---
