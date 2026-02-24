# #11 Disable C++11 code path in dynamic_vtable.hpp [Closed]

> Username: jzmaddock  
> Created at: 2016-07-04 17:07:23 UTC  
> Updated at: 2017-03-24 14:05:21 UTC  
> Closed at: 2017-03-24 14:05:20 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/11  

Without this there are test failures for dynamic_any_cast.  
Previously this path was disabled by virtue of BOOST_NO_CXX11_CONSTEXPR, but since constexpr works just fine in VC14up3 this path now gets enabled.  The failure has nothing to do with constexpr support though.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2016-07-04 17:12:46 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/11#issuecomment-230329528  

This is a quick workaround, rather than a full fix:  
  
Boost.Config has now been updated to not set BOOST_NO_CXX11_CONSTEXPR for VC14up3 which causes a new code path to be taken in this header.  The change completely breaks dynamic_any_cast for reasons I don't understand but which are completely unrelated to constexpr support.    
  
I suspect if you have a copy of VC14up3 handy then a spot of debugging will locate the cause of the failure and a better fix: for reasons I don't understand vtable lookup in dynamic_any_cast always fails.  Perhaps some other config macro needs checking in the PP logic?

---

## Comment 2

> Username: swatanabe  
> Created_at: 2017-03-24 14:05:20 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/11#issuecomment-289031398  

I've fixed this in develop.  The pack expansion is miscompiling for mysterious reasons.  I adjusted it to something that VC14 can handle.

---
