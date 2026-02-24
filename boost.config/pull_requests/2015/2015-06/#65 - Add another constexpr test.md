# #65 Add another constexpr test [Merged]

> Username: MarcelRaad  
> Created at: 2015-06-27 11:12:41 UTC  
> Updated at: 2015-06-27 15:11:08 UTC  
> Merged at: 2015-06-27 11:56:40 UTC  
> Closed at: 2015-06-27 11:56:40 UTC  
> Url: https://github.com/boostorg/config/pull/65  

MSVC 14 RC doesn't call the constexpr conversion operator in some cases.  
It complains:  
boost_no_constexpr.ipp(72): error C2051: case expression not constant

---
