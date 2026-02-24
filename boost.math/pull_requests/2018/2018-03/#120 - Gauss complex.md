# #120 Gauss complex [Merged]

> Username: NAThompson  
> Created at: 2018-03-18 13:02:25 UTC  
> Updated at: 2018-05-05 04:55:24 UTC  
> Merged at: 2018-05-01 18:06:47 UTC  
> Closed at: 2018-05-01 18:06:47 UTC  
> Url: https://github.com/boostorg/math/pull/120  

This isn't really even a pull request necessarily, it's just easier to communicate over a PR than over an email thread.  
  
Other that the trivial bug [here](https://github.com/boostorg/multiprecision/pull/49/commits/458e2b90da06b5357cdfdd32efd4a16dd34f019d), it looks like we can support functions f:Real->{Real or Complex} in Gaussian quadrature pretty easy. But we lose the ability to call the `integrate` function without an object, which presumably can be worked around, or just not supported anymore.  
  
It looks like `std::result_of_t` is deprecated in C++17, so I used `std::invoke_result_t`, which isn't supported on Clang in `c++1z` mode, so ostensibly, an `#ifdef` on the CPP version is required to get this to industrial strength.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-04-30 18:55:25 UTC  
> Url: https://github.com/boostorg/math/pull/120#issuecomment-385494591  

Looks good to me - made some changes to retain static integrate methods which seem to work OK - was there a reason for making them non-static?

---

## Comment 2

> Username: NAThompson  
> Created_at: 2018-05-01 02:33:42 UTC  
> Url: https://github.com/boostorg/math/pull/120#issuecomment-385584981  

I actually have no clue why I removed the static qualifiers! That was a mistake; nice catch.

---
