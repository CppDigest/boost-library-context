# #83 C++20/gcc10 removed allocator construct/destroy [Merged]

> Username: poelmanc  
> Created at: 2020-05-10 20:14:49 UTC  
> Updated at: 2020-05-11 17:53:05 UTC  
> Merged at: 2020-05-11 17:53:04 UTC  
> Closed at: 2020-05-11 17:53:04 UTC  
> Url: https://github.com/boostorg/ublas/pull/83  

std::allocator<T>::construct/destroy were deprecated in C++17 and removed in C++20, in favor of std::allocator_traits<T>::construct/destroy, which were introduced in C++11. (https://en.cppreference.com/w/cpp/memory/allocator_traits/construct). They are gone in gcc10. This fix switches to the the latter for C++17 and greater, enabling compilation with gcc 10+.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-05-10 23:42:11 UTC  
> Url: https://github.com/boostorg/ublas/pull/83#issuecomment-626407896  

# [Codecov](https://codecov.io/gh/boostorg/ublas/pull/83?src=pr&el=h1) Report  
> Merging [#83](https://codecov.io/gh/boostorg/ublas/pull/83?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/ublas/commit/8938db5ce586b842c0f740dd284f92ee057a40f0&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ublas/pull/83/graphs/tree.svg?width=650&height=150&src=pr&token=CShoBArRWq)](https://codecov.io/gh/boostorg/ublas/pull/83?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #83   +/-   ##  
========================================  
  Coverage    67.57%   67.57%             
========================================  
  Files           33       33             
  Lines         1437     1437             
  Branches       534      534             
========================================  
  Hits           971      971             
  Misses          69       69             
  Partials       397      397             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ublas/pull/83?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ublas/pull/83?src=pr&el=footer). Last update [8938db5...fde300a](https://codecov.io/gh/boostorg/ublas/pull/83?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: bassoy  
> Created_at: 2020-05-11 06:25:30 UTC  
> Url: https://github.com/boostorg/ublas/pull/83#issuecomment-626497351  

@poelmanc thanks for using Boost.uBlas. We are having a transition from matrix and vector to tensor representation. The code will be much clearer and usuable with newer C++ standards.  
Thanks again.

---

## Review 3 [Approved]

> Username: amitsingh19975  
> Created_at: 2020-05-11 09:34:13 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/83#pullrequestreview-409012234  

It looks good to me and from my perspective code looks clean. It's a welcome change from my side. Thank you @poelmanc for giving your time in improving Boost.uBlas.

---
