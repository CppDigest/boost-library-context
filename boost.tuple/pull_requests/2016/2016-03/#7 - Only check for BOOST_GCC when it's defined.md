# #7 Only check for BOOST_GCC when it's defined [Merged]

> Username: AMDmi3  
> Created at: 2016-03-01 10:54:32 UTC  
> Updated at: 2016-03-01 21:05:32 UTC  
> Merged at: 2016-03-01 16:38:58 UTC  
> Closed at: 2016-03-01 16:38:58 UTC  
> Url: https://github.com/boostorg/tuple/pull/7  

This fixes warnings with clang:  
  
/usr/local/include/boost/tuple/detail/tuple_basic.hpp:44:5: warning: 'BOOST_GCC' is not defined, evaluates to 0 [-Wundef]

---
