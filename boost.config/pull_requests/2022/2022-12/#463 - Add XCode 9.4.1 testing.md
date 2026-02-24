# #463 Add XCode 9.4.1 testing. [Merged]

> Username: jzmaddock  
> Created at: 2022-12-29 19:58:57 UTC  
> Updated at: 2022-12-30 12:26:59 UTC  
> Merged at: 2022-12-30 09:15:11 UTC  
> Closed at: 2022-12-30 09:15:11 UTC  
> Url: https://github.com/boostorg/config/pull/463  

Refs https://github.com/boostorg/config/issues/462.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2022-12-30 11:58:01 UTC  
> Url: https://github.com/boostorg/config/pull/463#issuecomment-1367880685  

Wouldn't the C++17 test be good way to ensure nothing is missed? E.g. one test fails due to missing `<tuple>` hence either there is a missing check of `BOOST_NO_CXX11_HDR_TUPLE` or it is wrongly not defined.  
  
I mean the purpose of Boost.Config (those macros) are to detect defects of the kind "not supported by the std lib" so IMO that test is valuable.

---
