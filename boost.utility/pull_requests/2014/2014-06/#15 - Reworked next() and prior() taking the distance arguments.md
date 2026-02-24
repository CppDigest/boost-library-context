# #15 Reworked next() and prior() taking the distance arguments. [Merged]

> Username: Lastique  
> Created at: 2014-06-23 21:17:15 UTC  
> Updated at: 2014-06-25 18:26:47 UTC  
> Merged at: 2014-06-25 18:26:44 UTC  
> Closed at: 2014-06-25 18:26:44 UTC  
> Url: https://github.com/boostorg/utility/pull/15  

The new version should provide the expected behavior in the case (prior(v.end(), v.size()) == v.begin()). It should also work with integers now, as was originally intended by David Abrahams. Added tests to verify these new use cases.  
  
See the relevant discussions:  
https://groups.google.com/forum/#!topic/boost-developers-archive/qZtIM6l9B0s  
http://lists.boost.org/Archives/boost/2004/02/61556.php

---
