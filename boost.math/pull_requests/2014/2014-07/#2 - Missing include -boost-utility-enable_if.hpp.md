# #2 Missing include <boost/utility/enable_if.hpp> [Closed]

> Username: rogiervd  
> Created at: 2014-07-09 19:08:48 UTC  
> Updated at: 2014-07-10 13:55:05 UTC  
> Closed at: 2014-07-10 13:54:58 UTC  
> Url: https://github.com/boostorg/math/pull/2  

I think there is a missing #include in bernoulli_details.hpp for enable_if_c. At least, the unit tests failed for me (Ubuntu 12.04) and this fixes it.

---
