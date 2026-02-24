# #21 Use BOOST_INLINE_CONSTEXPR for inline const variables [Merged]

> Username: matbech  
> Created at: 2021-09-04 18:37:08 UTC  
> Updated at: 2026-02-05 23:55:48 UTC  
> Merged at: 2026-02-05 23:55:47 UTC  
> Closed at: 2026-02-05 23:55:47 UTC  
> Url: https://github.com/boostorg/xpressive/pull/21  

This fixes the data duplication in translation units when compiling with C++17 or newer.  
Fixes issue https://github.com/boostorg/xpressive/issues/20

---
