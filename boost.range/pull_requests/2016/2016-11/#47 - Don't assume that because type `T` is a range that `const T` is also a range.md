# #47 Don't assume that because type `T` is a range that `const T` is also a range. [Merged]

> Username: ericniebler  
> Created at: 2016-11-18 19:51:05 UTC  
> Updated at: 2016-11-18 20:25:45 UTC  
> Merged at: 2016-11-18 20:06:32 UTC  
> Closed at: 2016-11-18 20:06:32 UTC  
> Url: https://github.com/boostorg/range/pull/47  

`size_type<const T>` erroneously dispatches to `detail::size_type_<T>`. That breaks for types that have non-const `begin`/`end` but that don't have const `begin`/`end`. The range-v3 library has such types.

---

## Comment 1

> Username: HighCommander4  
> Created_at: 2016-11-18 20:12:14 UTC  
> Url: https://github.com/boostorg/range/pull/47#issuecomment-261630250  

Out of curiosity, how interoperable is range-v3 meant to be with Boost.Range?

---

## Comment 2

> Username: ericniebler  
> Created_at: 2016-11-18 20:25:45 UTC  
> Url: https://github.com/boostorg/range/pull/47#issuecomment-261633197  

Largely untested and not a _huge_ priority for me, but I'll fix the problems as they come up.

---
