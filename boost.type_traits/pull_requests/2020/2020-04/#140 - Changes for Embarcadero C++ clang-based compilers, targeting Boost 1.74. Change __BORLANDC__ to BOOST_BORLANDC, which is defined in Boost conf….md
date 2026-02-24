# #140 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74. Change __BORLANDC__ to BOOST_BORLANDC, which is defined in Boost conf… [Merged]

> Username: eldiener  
> Created at: 2020-04-08 16:42:53 UTC  
> Updated at: 2020-05-06 19:30:11 UTC  
> Merged at: 2020-05-06 19:30:11 UTC  
> Closed at: 2020-05-06 19:30:11 UTC  
> Url: https://github.com/boostorg/type_traits/pull/140  

…ig for the Embarcadero non-clang-based compilers.

---

## Comment 1

> Username: eldiener  
> Created_at: 2020-05-06 13:30:15 UTC  
> Url: https://github.com/boostorg/type_traits/pull/140#issuecomment-624650457  

Can this please be merged ?

---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2020-05-06 16:32:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/type_traits/pull/140#pullrequestreview-406787803  

📁 include/boost/type_traits/detail/config.hpp

```diff
  50 | //
  51 |- #if defined(_MSC_EXTENSIONS) && !defined(__BORLANDC__)
  51 |+ #if defined(_MSC_EXTENSIONS) && !defined(BOOST_BORLANDC)
```

> Username: jzmaddock  
> Created_at: 2020-05-06 16:32:42 UTC  
> Url: https://github.com/boostorg/type_traits/pull/140#discussion_r420928958  

Has this been tested?  Does the clang based compiler support msvc calling conventions?  Just double checking....

> Username: eldiener  
> Created_at: 2020-05-06 17:29:51 UTC  
> Updated_at: 2020-05-06 17:29:52 UTC  
> Url: https://github.com/boostorg/type_traits/pull/140#discussion_r420966026  

The tests all pass. Clang says it supports the msvc calling conventions.


---
