# #135 Deprecate `boost::core::is_same` and the associated header [Merged]

> Username: Lastique  
> Created at: 2022-12-22 12:56:20 UTC  
> Updated at: 2022-12-22 15:54:51 UTC  
> Merged at: 2022-12-22 15:54:29 UTC  
> Closed at: 2022-12-22 15:54:30 UTC  
> Url: https://github.com/boostorg/core/pull/135  

Moved `is_same` implementation to detail (both directory and namespace) to use in the public headers and avoid introducing new dependencies. All other uses were ported to Boost.TypeTraits. Likewise, the documentation now recommends users to use Boost.TypeTraits or C++ standard library instead.  
  
Also, removed unnecessary includes and added missing ones in a few places.

---

## Review 1 [Commented]

> Username: glenfe  
> Created_at: 2022-12-22 13:53:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/135#pullrequestreview-1227703007  

📁 test/allocator_difference_type_test.cpp

```diff
   9 |- #include <boost/core/is_same.hpp>
   9 | #include <boost/core/lightweight_test_trait.hpp>
  10 |+ #include <boost/type_traits/is_same.hpp>
```

> Username: glenfe  
> Created_at: 2022-12-22 13:53:37 UTC  
> Updated_at: 2022-12-22 13:53:44 UTC  
> Url: https://github.com/boostorg/core/pull/135#discussion_r1055482879  

I would prefer if these unit tests in Core used boost::core::detail::is_same instead of requiring TypeTraits

> Username: pdimov  
> Created_at: 2022-12-22 13:58:27 UTC  
> Updated_at: 2022-12-22 13:58:28 UTC  
> Url: https://github.com/boostorg/core/pull/135#discussion_r1055488191  

Or maybe just change to BOOST_TEST_TRAIT_SAME.

> Username: Lastique  
> Created_at: 2022-12-22 13:59:33 UTC  
> Url: https://github.com/boostorg/core/pull/135#discussion_r1055489183  

Boost.TypeTraits is already required by some tests, so you're not gaining anything by using `core::detail::is_same`. I chose Boost.TypeTraits because that is what we recommend in docs. But I can change it if you want.

> Username: Lastique  
> Created_at: 2022-12-22 14:02:56 UTC  
> Url: https://github.com/boostorg/core/pull/135#discussion_r1055493464  

I thought about `BOOST_TEST_TRAIT_SAME` but it seems to require variadic macros, i.e. C++11.

> Username: pdimov  
> Created_at: 2022-12-22 14:10:23 UTC  
> Updated_at: 2022-12-22 14:10:24 UTC  
> Url: https://github.com/boostorg/core/pull/135#discussion_r1055500047  

Variadic macros are supported as an extension by all compilers we care about, see https://github.com/boostorg/core/blob/develop/test/lightweight_test_test4.cpp.

> Username: Lastique  
> Created_at: 2022-12-22 14:23:11 UTC  
> Url: https://github.com/boostorg/core/pull/135#discussion_r1055514875  

I know at least clang issues warnings about using variadic macros in C++03 mode. I think, recent gcc versions do too. I'd rather not mess with warnings.

> Username: Lastique  
> Created_at: 2022-12-22 14:27:11 UTC  
> Url: https://github.com/boostorg/core/pull/135#discussion_r1055518937  

@glenfe Done. The `allocator_size_type_test.cpp` test I left using `boost::is_same` because it needs `boost::make_unsigned` anyway.

> Username: pdimov  
> Created_at: 2022-12-22 14:40:14 UTC  
> Updated_at: 2022-12-22 14:40:15 UTC  
> Url: https://github.com/boostorg/core/pull/135#discussion_r1055531127  

See https://github.com/boostorg/core/blob/75c765cc1333e7656c52feddace1f303e53419a3/test/Jamfile.v2#L121-L122, https://github.com/boostorg/core/blob/75c765cc1333e7656c52feddace1f303e53419a3/test/Jamfile.v2#L100-L103, and https://github.com/boostorg/core/blob/75c765cc1333e7656c52feddace1f303e53419a3/include/boost/core/lightweight_test_trait.hpp#L84-L87.

> Username: Lastique  
> Created_at: 2022-12-22 14:51:08 UTC  
> Url: https://github.com/boostorg/core/pull/135#discussion_r1055541116  

I think I'll leave it as is, unless Glen wants me to change it again.


---

## Review 2 [Approved]

> Username: glenfe  
> Created_at: 2022-12-22 15:25:51 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/core/pull/135#pullrequestreview-1227842428  

Looks good to me

---
