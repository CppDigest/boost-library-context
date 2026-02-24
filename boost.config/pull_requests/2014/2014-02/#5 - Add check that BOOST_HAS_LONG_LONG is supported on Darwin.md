# #5 Add check that BOOST_HAS_LONG_LONG is supported on Darwin [Merged]

> Username: Belcourt  
> Created at: 2014-02-05 00:34:55 UTC  
> Updated at: 2014-02-05 17:32:07 UTC  
> Merged at: 2014-02-05 17:32:07 UTC  
> Closed at: 2014-02-05 17:32:07 UTC  
> Url: https://github.com/boostorg/config/pull/5  

before enabling the macro.  Apple helpfully supplies this  
macro (__DARWIN_NO_LONG_LONG) if long long type is supported.  
This to handle case where compiling with -pedantic causes  
compiler errors because it enforces ISO compliance  
(c++98 in this case).  
  
This will fix several tests in develop and master and will clean conversion tests completely on Mac.

---
