# #130 Added workaround for GCC 3.4 due to ambiguous. [Merged]

> Username: Flast  
> Created at: 2016-03-16 12:15:13 UTC  
> Updated at: 2016-03-16 13:40:25 UTC  
> Merged at: 2016-03-16 13:36:54 UTC  
> Closed at: 2016-03-16 13:36:54 UTC  
> Url: https://github.com/boostorg/fusion/pull/130  

This PR will fix compile failure on GCC 3.4, like [convert_boost_tuple / gcc-mngw-3.4c+](http://www.boost.org/development/tests/develop/developer/output/igaztanaga-gcc-3-4c++03-boost-bin-v2-libs-fusion-test-convert_boost_tuple-test-gcc-mngw-3-4c+-dbg-dbg-symbl-off.html).  
- Tested compiler  
  - GCC 3.4.2 (Fedora Core 3, gcc-3.4.2-6.fc3.x86_64)  
  - GCC 4.9.3 (Cygwin x86_64) gnu++98 / gnu++11 / gnu++14  
  - MSVC 12 (VS2013 update5)  
  
NOTE: GCC 3.3 and earlier are not supported officially by Boost community.

---
