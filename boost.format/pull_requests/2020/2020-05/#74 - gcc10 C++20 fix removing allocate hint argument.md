# #74 gcc10 C++20 fix removing allocate hint argument [Closed]

> Username: poelmanc  
> Created at: 2020-05-10 06:27:59 UTC  
> Updated at: 2020-05-13 17:54:15 UTC  
> Closed at: 2020-05-13 17:54:14 UTC  
> Url: https://github.com/boostorg/format/pull/74  

The std::allocator<T>::allocate(n, hint) overload was deprecated in C++17 and removed in C++20 (https://en.cppreference.com/w/cpp/memory/allocator/allocate). This fix calls the single-argument allocate(n) function for C++17 and greater.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-05-10 14:16:40 UTC  
> Updated_at: 2020-05-10 14:17:07 UTC  
> Url: https://github.com/boostorg/format/pull/74#issuecomment-626335035  

# [Codecov](https://codecov.io/gh/boostorg/format/pull/74?src=pr&el=h1) Report  
> Merging [#74](https://codecov.io/gh/boostorg/format/pull/74?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/format/commit/f2de3112a5259966d74cec65adfa4986ec07bf66&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/format/pull/74/graphs/tree.svg?width=650&height=150&src=pr&token=9WwfaSgmDb)](https://codecov.io/gh/boostorg/format/pull/74?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #74   +/-   ##  
========================================  
  Coverage    64.33%   64.33%             
========================================  
  Files           10       10             
  Lines          816      816             
  Branches       290      290             
========================================  
  Hits           525      525             
  Misses         125      125             
  Partials       166      166             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/format/pull/74?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/format/alt\_sstream\_impl.hpp](https://codecov.io/gh/boostorg/format/pull/74/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9mb3JtYXQvYWx0X3NzdHJlYW1faW1wbC5ocHA=) | `22.58% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/format/pull/74?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/format/pull/74?src=pr&el=footer). Last update [f2de311...08041d9](https://codecov.io/gh/boostorg/format/pull/74?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: mgaunard  
> Created_at: 2020-05-13 17:19:18 UTC  
> Url: https://github.com/boostorg/format/pull/74#issuecomment-628130936  

I hadn't seen this and did a similar change in #75   
This PR however is missing one case.  
  
I also don't think it's worth special-casing for C++14 and older.

---

## Comment 3

> Username: poelmanc  
> Created_at: 2020-05-13 17:54:14 UTC  
> Url: https://github.com/boostorg/format/pull/74#issuecomment-628149813  

Thanks, I like your simpler fix.

---
