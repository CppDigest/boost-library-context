# #101 Fix MSVC compilation without /arch:SSE2 [Closed]

> Username: MarcelRaad  
> Created at: 2019-01-03 13:09:24 UTC  
> Updated at: 2019-01-06 11:37:05 UTC  
> Closed at: 2019-01-06 08:37:33 UTC  
> Url: https://github.com/boostorg/type_traits/pull/101  

When compiling with /arch:IA32, trying to use `__vectorcall` results in  
compiler error C2218 [0]. Use the predefined `_M_IX86_FP` macro [1] to  
only use the `__vectorcall` specializations when targeting SSE2 or  
higher.  
  
[0] https://docs.microsoft.com/en-us/cpp/error-messages/compiler-errors-1/compiler-error-c2218  
[1] https://docs.microsoft.com/en-us/cpp/preprocessor/predefined-macros

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-01-03 17:54:12 UTC  
> Url: https://github.com/boostorg/type_traits/pull/101#issuecomment-451223760  

Looks good, but see: https://github.com/boostorg/type_traits/pull/102

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2019-01-06 08:37:33 UTC  
> Url: https://github.com/boostorg/type_traits/pull/101#issuecomment-451724947  

I fixed this slightly differently in https://github.com/boostorg/type_traits/pull/102, we have tests now as well.

---

## Comment 3

> Username: MarcelRaad  
> Created_at: 2019-01-06 11:36:55 UTC  
> Url: https://github.com/boostorg/type_traits/pull/101#issuecomment-451734802  

Great, thanks!

---
