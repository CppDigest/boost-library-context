# #51 Make zip_iterator take more than three elements [Merged]

> Username: ddemidov  
> Created at: 2014-01-09 19:00:29 UTC  
> Updated at: 2014-07-26 06:17:54 UTC  
> Merged at: 2014-01-09 19:57:39 UTC  
> Closed at: 2014-01-09 19:57:39 UTC  
> Url: https://github.com/boostorg/compute/pull/51  

This uses Boost.Preprocessor macros to allow zip iterators to work with arbitrary number of elements (the current limit is maximum `boost::tuple` size which is 10 by default).  
  
It seems that possible implementation with variadic templates would be more cumbersome, as it would require recursion for things like [this](https://github.com/kylelutz/compute/blob/43678410be226caf3f7033b817a2a9fd8d6eb00b/include/boost/compute/iterator/zip_iterator.hpp#L166-L168).  
  
I also tried adding `detail::is_tuple<T>`, and replacing multiple [operator<<](https://github.com/kylelutz/compute/blob/43678410be226caf3f7033b817a2a9fd8d6eb00b/include/boost/compute/iterator/zip_iterator.hpp#L314-L331) with a single one which would be guarded by `boost::enable_if`, but I run into ambiguity errors, so I gave up (but this approach worked [here](https://github.com/kylelutz/compute/blob/43678410be226caf3f7033b817a2a9fd8d6eb00b/include/boost/compute/iterator/zip_iterator.hpp#L95-L116)).  
  
See also #50

---

## Comment 1

> Username: ddemidov  
> Created_at: 2014-01-09 19:15:55 UTC  
> Url: https://github.com/boostorg/compute/pull/51#issuecomment-31965813  

Forgot to deal with `types/tuple.hpp`. Wait for it...

---

## Comment 2

> Username: ddemidov  
> Created_at: 2014-01-09 19:43:24 UTC  
> Url: https://github.com/boostorg/compute/pull/51#issuecomment-31968342  

Done.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-01-09 19:57:41 UTC  
> Url: https://github.com/boostorg/compute/pull/51#issuecomment-31969621  

Awesome! Thanks!

---
