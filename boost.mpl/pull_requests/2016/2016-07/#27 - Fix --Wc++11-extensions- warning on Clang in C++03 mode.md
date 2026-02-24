# #27 Fix "-Wc++11-extensions" warning on Clang in C++03 mode [Merged]

> Username: morinmorin  
> Created at: 2016-07-02 05:34:33 UTC  
> Updated at: 2017-03-11 04:24:32 UTC  
> Merged at: 2016-07-12 20:43:45 UTC  
> Closed at: 2016-07-12 20:43:45 UTC  
> Url: https://github.com/boostorg/mpl/pull/27  

Including `boost/mpl/print.hpp` on Clang in C++03 mode **always** produces a warning (i.e. even without using `boost::mpl::print<T>`).  
  
We can remove the warning by adding   
  
```  
#pragma clang diagnostic ignored "-Wc++11-extensions"  
```  
  
Note that `-Wc++11-extensions` is not supported in Clang older than 3.0, but this is not an issue since the current `boost::mpl::print` implementation requires Clang 3.0 or newer versions and results in errors in Clang older than 3.0.   
  
This issue was reported by Richard Hadsell on Boost developer ML.

---
