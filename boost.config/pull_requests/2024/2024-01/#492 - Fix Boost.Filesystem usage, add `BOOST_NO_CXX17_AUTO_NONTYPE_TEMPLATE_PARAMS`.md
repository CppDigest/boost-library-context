# #492 Fix Boost.Filesystem usage, add `BOOST_NO_CXX17_AUTO_NONTYPE_TEMPLATE_PARAMS` [Merged]

> Username: Lastique  
> Created at: 2024-01-22 13:27:14 UTC  
> Updated at: 2024-02-04 09:46:55 UTC  
> Merged at: 2024-01-26 16:25:51 UTC  
> Closed at: 2024-01-26 16:25:51 UTC  
> Url: https://github.com/boostorg/config/pull/492  

This PR:  
  
* Updates `tools/generate.cpp` to avoid using Boost.Filesystem APIs that were deprecated and now removed.  
* Adds a new `BOOST_NO_CXX17_AUTO_NONTYPE_TEMPLATE_PARAMS` macro that indicates whether the compiler does not support C++17 [`auto` non-type template parameters](https://en.cppreference.com/w/cpp/language/template_parameters).

---

## Comment 1

> Username: Lastique  
> Created_at: 2024-01-26 08:49:27 UTC  
> Url: https://github.com/boostorg/config/pull/492#issuecomment-1911686464  

Ping @jzmaddock. Note that at least the first commit needs to be merged, as currently `tools/generate.cpp` fails to compile.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-01-26 16:25:32 UTC  
> Url: https://github.com/boostorg/config/pull/492#issuecomment-1912336670  

Failure are due to b2 dropping GCC-4 support, merging...

---

## Comment 3

> Username: Lastique  
> Created_at: 2024-01-26 16:57:40 UTC  
> Url: https://github.com/boostorg/config/pull/492#issuecomment-1912381210  

Thanks. Drone failure is because of a `b2` [issue](https://github.com/bfgroup/b2/issues/360), which was reportedly fixed.

---

## Comment 4

> Username: Lastique  
> Created_at: 2024-02-02 00:47:00 UTC  
> Url: https://github.com/boostorg/config/pull/492#issuecomment-1922584455  

Please, also merge to master as there is downstream code that uses this macro that I would like to merge as well.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2024-02-04 09:46:54 UTC  
> Url: https://github.com/boostorg/config/pull/492#issuecomment-1925665223  

Done.

---
