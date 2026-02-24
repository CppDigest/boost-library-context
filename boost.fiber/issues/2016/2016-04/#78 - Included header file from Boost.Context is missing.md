# #78 - Included header file from Boost.Context is missing. [Closed]

> Username: ClaymorePT  
> Created at: 2016-04-05 01:23:56 UTC  
> Updated at: 2016-04-05 09:36:45 UTC  
> Closed at: 2016-04-05 04:31:31 UTC  
> Url: https://github.com/boostorg/fiber/issues/78  

I was trying to build Fiber for Boost 1.60.0 when the following error occurred.  
  
In file included from libs/fiber/src/algorithm.cpp:9:0:  
./boost/fiber/context.hpp:20:42: fatal error: boost/context/detail/apply.hpp: No such file or directory  
compilation terminated.  
It seems the header "boost/context/detail/apply.hpp" was removed from boost context.  
Is this an issue for fiber?  
  
Thank you.

---

## Comment 1

> Username: olk  
> Created at: 2016-04-05 04:31:31 UTC  
> Updated at: 2016-04-05 04:31:58 UTC  
> Url: https://github.com/boostorg/fiber/issues/78#issuecomment-205639872  

boost.fiber is still in development - you need to fetch the version 1.61 (== branch develop) of boost.context. but boost/context/detail/apply.hpp is the correct path to this file - maybe you need to check your compiler include paths. additionally, boost.context required C++11

---

## Comment 2

> Username: ClaymorePT  
> Created at: 2016-04-05 09:36:44 UTC  
> Url: https://github.com/boostorg/fiber/issues/78#issuecomment-205730835  

Oh, I didn't notice it required Boost 1.61.   
Boost 1.61 is only supposed to come out by the end of this month.  
  
Thank you for the information.
