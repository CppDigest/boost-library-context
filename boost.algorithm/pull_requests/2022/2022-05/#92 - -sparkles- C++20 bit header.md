# #92 :sparkles: C++20 bit header [Closed]

> Username: jgopel  
> Created at: 2022-05-05 20:40:49 UTC  
> Updated at: 2022-05-05 23:42:08 UTC  
> Closed at: 2022-05-05 20:51:19 UTC  
> Url: https://github.com/boostorg/algorithm/pull/92  

As part of C++Now's Library in a Week effort.  
  
Thanks to @fpikus for the help with this!

---

## Comment 1

> Username: glenfe  
> Created_at: 2022-05-05 20:49:44 UTC  
> Url: https://github.com/boostorg/algorithm/pull/92#issuecomment-1119034294  

We already have this in Boost.Core

---

## Comment 2

> Username: glenfe  
> Created_at: 2022-05-05 20:51:19 UTC  
> Url: https://github.com/boostorg/algorithm/pull/92#issuecomment-1119035307  

See https://github.com/boostorg/core/blob/develop/include/boost/core/bit.hpp  
  
And https://www.boost.org/doc/libs/1_79_0/libs/core/doc/html/core/bit.html  
  
We don't need a duplicate in Boost.Algorithm.

---

## Comment 3

> Username: mclow  
> Created_at: 2022-05-05 22:36:06 UTC  
> Url: https://github.com/boostorg/algorithm/pull/92#issuecomment-1119104335  

> On May 5, 2022, at 2:51 PM, Glen Fernandes ***@***.***> wrote:  
>   
> We don't need a duplicate in Boost.Algorithm.  
  
How do you feel about a PR adding constexpr, etc into the bit routines in Boost.core?

---

## Comment 4

> Username: glenfe  
> Created_at: 2022-05-05 22:43:52 UTC  
> Url: https://github.com/boostorg/algorithm/pull/92#issuecomment-1119108166  

Any improvements to the functionality in Core are always good.  
  
On a related note, it seemed like a few C++Now's ago, everyone was excited about a future Boost that didn't replicate functionality available in the C++ standard library. Has that changed, or does the sentiment not apply to C++20 and above?

---

## Comment 5

> Username: pdimov  
> Created_at: 2022-05-05 23:42:08 UTC  
> Url: https://github.com/boostorg/algorithm/pull/92#issuecomment-1119142459  

`constexpr` when using non-constexpr intrinsics isn't possible without `std::is_constant_evaluated`, which is C++20. There's an issue already about this (https://github.com/boostorg/core/issues/109) and I'll probably end up adding separately named functions when `constexpr` is required. (The existing functions are best-effort constexpr, which doesn't allow their portable use in constant expressions.)

---
