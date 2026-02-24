# #56 Define BOOST_SYSTEM_DEPRECATED for other compilers [Merged]

> Username: tanzislam  
> Created at: 2020-12-24 00:00:01 UTC  
> Updated at: 2020-12-24 00:48:54 UTC  
> Merged at: 2020-12-24 00:48:14 UTC  
> Closed at: 2020-12-24 00:48:14 UTC  
> Url: https://github.com/boostorg/system/pull/56  

Currently compilation fails with Oracle Developer Studio 12.6, for example:  
  
    $ ./b2 toolset=sun threading=multi variant=debug link=static cxxflags=-std=c++11 --with-system  
    ...  
    "./boost/system/detail/error_condition.hpp", line 96: Error: BOOST_SYSTEM_DEPRECATED is not defined.  
    "./boost/system/detail/error_condition.hpp", line 96: Error: No direct declarator preceding ""this function is slated for removal"".  
    "./boost/system/detail/error_condition.hpp", line 96: Error: No direct declarator preceding "(".

---

## Comment 1

> Username: pdimov  
> Created_at: 2020-12-24 00:48:54 UTC  
> Url: https://github.com/boostorg/system/pull/56#issuecomment-750660574  

Thanks.

---
