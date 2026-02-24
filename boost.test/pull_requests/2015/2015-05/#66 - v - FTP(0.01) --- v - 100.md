# #66 v * FTP(0.01)  -->  v / 100 [Merged]

> Username: akrzemi1  
> Created at: 2015-05-26 08:31:43 UTC  
> Updated at: 2015-05-28 06:42:06 UTC  
> Merged at: 2015-05-28 06:42:05 UTC  
> Closed at: 2015-05-28 06:42:05 UTC  
> Url: https://github.com/boostorg/test/pull/66  

Rationale:  
Because we are not using the literal of type double, we avoid the bug where `FTP(0.01)` is evaluated as `FTP(int(0.01))` for FPT types that do not offer a conversion from `double`.  
I do not use an explicit cast `v / FPT(100)` in order to allow the compiler to see that we are dividing by the integer, and engage optimizations for that case, if any.  
By `v / 100` I require that conversion from int to FPT is implicit, and thereby avoid executing explicit constructors, which may have non-conversion semantics.  
To the best of my knowledge, `v / 100` is not less accurate than `v * 0.01`. It may be slower, but given that this is evaluated in BOOST_TEST, which does lots of ops on IO streams, this should be negligible; plus, we are affecting only a percentage tolerance manipulator -- not the floating-point comparison itself.

---

## Comment 1

> Username: akrzemi1  
> Created_at: 2015-05-27 06:33:49 UTC  
> Url: https://github.com/boostorg/test/pull/66#issuecomment-105774383  

Are you OK with this change? Can we merge it? This is the last one that handles all the FP issues (in a minimalistic way).

---
