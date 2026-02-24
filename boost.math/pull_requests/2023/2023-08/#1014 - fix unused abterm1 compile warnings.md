# #1014 fix unused abterm1 compile warnings [Merged]

> Username: ryanelandt  
> Created at: 2023-08-18 20:40:27 UTC  
> Updated at: 2023-08-19 14:03:29 UTC  
> Merged at: 2023-08-19 11:22:21 UTC  
> Closed at: 2023-08-19 11:22:21 UTC  
> Url: https://github.com/boostorg/math/pull/1014  

The file `exp_sinh_detail.hpp` contains an unconditionally unused variable `abterm1` that causes compile warnings. This issue was fixed for C++17 and above in #884 with the addition of the `BOOST_MATH_MAYBE_UNUSED` macro.   
  
**This PR** implements a direct fix that removes the warning for all C++ versions. It also removes the `BOOST_MATH_MAYBE_UNUSED` macro as this is the only place it's used in the Boost (all libraries).

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-08-19 11:22:13 UTC  
> Url: https://github.com/boostorg/math/pull/1014#issuecomment-1684922059  

Looks good to me, not sure how that crept in there.... merging.

---

## Comment 2

> Username: ryanelandt  
> Created_at: 2023-08-19 14:03:26 UTC  
> Url: https://github.com/boostorg/math/pull/1014#issuecomment-1684968841  

Thanks!

---
