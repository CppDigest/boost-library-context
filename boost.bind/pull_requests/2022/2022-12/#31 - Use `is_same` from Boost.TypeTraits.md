# #31 Use `is_same` from Boost.TypeTraits [Merged]

> Username: Lastique  
> Created at: 2022-12-22 23:03:24 UTC  
> Updated at: 2022-12-23 11:33:07 UTC  
> Merged at: 2022-12-23 03:32:32 UTC  
> Closed at: 2022-12-23 03:32:32 UTC  
> Url: https://github.com/boostorg/bind/pull/31  

`boost::core::is_same` is deprecated, so use the one from Boost.TypeTraits.  
  
Note: This introduces a dependency on Boost.TypeTraits. I figured, this is better than duplicating `is_same` or depending on `boost::core::detail::is_same`. Boost.TypeTraits is a fairly lightweight nowdays. If you prefer a different solution, let me know.

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-12-22 23:09:35 UTC  
> Url: https://github.com/boostorg/bind/pull/31#issuecomment-1363426005  

Since Bind only depends on Core, and since people tend to dislike new dependencies in core libraries, it's probably better to use `core::detail::is_same` here (or a local copy).

---

## Comment 2

> Username: Lastique  
> Created_at: 2022-12-22 23:28:45 UTC  
> Url: https://github.com/boostorg/bind/pull/31#issuecomment-1363435688  

Ok, done. Note however, if we're going to phase away `enable_if` from Boost.Core in favor of Boost.TypeTraits, we will have this problem again, and `enable_if` is not quite as simple to duplicate.

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-12-22 23:33:03 UTC  
> Url: https://github.com/boostorg/bind/pull/31#issuecomment-1363437856  

It's actually pretty easy. https://github.com/boostorg/type_traits/blob/develop/include/boost/type_traits/enable_if.hpp  
  
Core's enable_if has a different interface from the standard/type_traits one, though, so I doubt we'll be able to phase it out.

---
