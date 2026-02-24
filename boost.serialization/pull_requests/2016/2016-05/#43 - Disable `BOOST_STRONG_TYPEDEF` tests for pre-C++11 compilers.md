# #43 Disable `BOOST_STRONG_TYPEDEF` tests for pre-C++11 compilers [Closed]

> Username: praetorian20  
> Created at: 2016-05-31 18:30:53 UTC  
> Updated at: 2017-01-30 16:27:48 UTC  
> Closed at: 2017-01-30 16:27:40 UTC  
> Url: https://github.com/boostorg/serialization/pull/43  

The previous commit added `noexcept` specification for  
`BOOST_STRONG_TYPEDEF`, but the unit test added didn't consider  
pre-C++11 compilers where `noexcept` is not available. The  
`static_assert`s in the unit test have now been disabled for these  
compilers.

---

## Comment 1

> Username: praetorian20  
> Created_at: 2016-05-31 18:32:29 UTC  
> Url: https://github.com/boostorg/serialization/pull/43#issuecomment-222778840  

This should address the test failures in #40. I just disabled all the `static_assert`s in the unit tests for pre-C++11 compilers.

---
