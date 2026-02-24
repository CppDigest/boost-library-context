# #10 Correct make_odd return type inconsistency [Merged]

> Username: Lastique  
> Created at: 2017-08-19 22:58:26 UTC  
> Updated at: 2017-08-27 16:29:23 UTC  
> Merged at: 2017-08-27 16:07:51 UTC  
> Closed at: 2017-08-27 16:07:51 UTC  
> Url: https://github.com/boostorg/integer/pull/10  

- Make `gcd_traits<signed char>::make_odd` return `unsigned int`, similar to all other `gcd_traits` specializations.  
- Correct the local variable types that receive the result of `make_odd` to avoid warnings about unintended conversion.

---
