# #95 Fix compilation failure with clang C++03 and Boost.Optional [Merged]

> Username: Lastique  
> Created at: 2018-11-04 12:50:37 UTC  
> Updated at: 2018-11-05 19:12:15 UTC  
> Merged at: 2018-11-05 18:52:58 UTC  
> Closed at: 2018-11-05 18:52:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/95  

This PR fixes compilation failures with clang in C++03 mode that were found in https://github.com/boostorg/integer/pull/11#issuecomment-435649481. Two problems are fixed here:  
  
- `is_explicitly_convertible` trait required a public destructor in C++03 mode because `declval` returns `T` by value in this mode. The compilation fails because `optional_base`, on which the trait gets instantiated during `optional` constructor compilation, has a protected destructor.  
- `is_byte_container` trait required a public `value_type` typedef in the range type. `optional_base` defines a protected typedef `value_type`. In general, this typedef is quite commonly used in non-range types, so don't use it to detect container/range types.  
  
More details are available in commit messages.  
  
Also, the PR adds a few missing includes and fixes a gcc warning about implicit fallthough in switch/case. It also adds a test for compatibility between Boost.Multiprecision and Boost.Optional.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-11-05 18:53:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/95#issuecomment-435991010  

Thanks for sorting that one out!  Merged.

---
