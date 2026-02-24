# #299 Remove sign compare -Wall warn on Apple clang [Merged]

> Username: ckormanyos  
> Created at: 2021-02-22 09:27:23 UTC  
> Updated at: 2021-02-22 12:06:22 UTC  
> Merged at: 2021-02-22 11:34:37 UTC  
> Closed at: 2021-02-22 11:34:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/299  

Simple casting should eliminate this warning

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-02-22 10:01:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/299#issuecomment-783252681  

CI is revealing a potentially unrelated (maybe new) problem, arising on Ubuntu, suite `functions_and_limits`.  
Will investigate root cause.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2021-02-22 10:59:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/299#issuecomment-783289410  

CI is re-running.  
  
Have now dealt with selected forgotten instances needing replacement of `boost::integral_constant` with `std::integral_constant` in `mpfi.hpp`.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2021-02-22 11:33:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/299#issuecomment-783310124  

OK. This one is now green

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2021-02-22 11:34:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/299#issuecomment-783310943  

Fix #298 and consistent std::integral_constant

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2021-02-22 11:34:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/299#issuecomment-783311000  

Fix #298 and consistent std::integral_constant

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2021-02-22 11:36:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/299#issuecomment-783311978  

Thanks Chris!

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2021-02-22 12:06:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/299#issuecomment-783327835  

Many thanks also to you and the colleagues as well. It is really effective and enjoyable to work with all this great C++11 and more efficient CI systems!

---
