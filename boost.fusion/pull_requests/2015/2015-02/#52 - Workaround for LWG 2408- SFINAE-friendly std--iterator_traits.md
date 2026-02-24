# #52 Workaround for LWG 2408: SFINAE-friendly std::iterator_traits. [Merged]

> Username: Flast  
> Created at: 2015-02-05 14:07:32 UTC  
> Updated at: 2015-02-09 11:20:26 UTC  
> Merged at: 2015-02-08 22:53:12 UTC  
> Closed at: 2015-02-08 22:53:12 UTC  
> Url: https://github.com/boostorg/fusion/pull/52  

Fix [std_tuple_iterator / msvc-12.0](http://www.boost.org/development/tests/develop/developer/output/teeks99-08e-win2012R2-64on64-boost-bin-v2-libs-fusion-test-std_tuple_iterator-test-msvc-12-0-debug-threading-multi.html) and [std_tuple_iterator / gcc-4.4.7](http://www.boost.org/development/tests/develop/developer/output/Sandia-gcc-4-4-7-c++0x-boost-bin-v2-libs-fusion-test-std_tuple_iterator-test-gcc-4-4-7-debug.html).  
  
SFINAE-friendly std::iterator_traits is now available for GCC(libstdc++v3) < 4.5 and MSVC 12.0. It means, there is no ambiguous about calling next/prior/... via ADL.  
- Tested:  
  - MSVC 9.0(2008) / 10.0(2010) / 11.0(2012u4) / 12.0(2013u4) / 14.0(2015 Preview)  
  - GCC 4.9.2 C++03 / C++11 / C++1y  
  - LLVM Clang 3.5.0 C++03 / C++11 / C++14

---
