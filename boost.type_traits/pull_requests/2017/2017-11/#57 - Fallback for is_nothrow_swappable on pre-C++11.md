# #57 Fallback for is_nothrow_swappable on pre-C++11 [Merged]

> Username: danieljames  
> Created at: 2017-11-29 18:06:02 UTC  
> Updated at: 2018-01-25 10:44:13 UTC  
> Merged at: 2018-01-25 10:44:13 UTC  
> Closed at: 2018-01-25 10:44:13 UTC  
> Url: https://github.com/boostorg/type_traits/pull/57  

Add an implementation of is_nothrow_swappable for when C++11 isn't available, as discussed in #40. True for non-const scalar types as I don't think their swaps can ever throw, and false_for everything else.  
  
Boost.Move's implementation uses `is_empty<T>::value || is_pod<T>::value` which is probably reasonable, but custom types might have an overloaded swap which is not `noexcept`, so that would be true in C++03, and false in C++11, which seems wrong to me.

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-11-29 21:55:05 UTC  
> Url: https://github.com/boostorg/type_traits/pull/57#issuecomment-348009425  

Looks good to me, FWIW.

---
