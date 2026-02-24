# #15 Fix warnings about redefining a defined macro [Merged]

> Username: Lastique  
> Created at: 2014-06-22 15:13:20 UTC  
> Updated at: 2014-06-22 15:41:34 UTC  
> Merged at: 2014-06-22 15:41:34 UTC  
> Closed at: 2014-06-22 15:41:34 UTC  
> Url: https://github.com/boostorg/thread/pull/15  

The warnings appear when a dependent project (e.g. Boost.Log tests and examples) builds with link=shared and defines BOOST_ALL_DYN_LINK macro. In this case BOOST_THREAD_USE_DLL is defined by both thread/build/Jamfile.v2 and boost/thread/detail/config.hpp. This commit makes sure that doesn't happen.

---
