# #49 Fix two major MSVC issues. [Merged]

> Username: Flast  
> Created at: 2015-01-21 14:38:33 UTC  
> Updated at: 2015-02-09 11:20:29 UTC  
> Merged at: 2015-02-08 22:53:57 UTC  
> Closed at: 2015-02-08 22:53:57 UTC  
> Url: https://github.com/boostorg/fusion/pull/49  

Those commits will fix two test case failures: [as_deque / msvc](http://www.boost.org/development/tests/develop/developer/output/teeks99-08e-win2012R2-64on64-boost-bin-v2-libs-fusion-test-as_deque-test-msvc-12-0-debug-threading-multi.html) and [map_move / msvc](http://www.boost.org/development/tests/develop/developer/output/teeks99-08e-win2012R2-64on64-boost-bin-v2-libs-fusion-test-map_move-test-msvc-12-0-debug-threading-multi.html).  
  
Tested under:  
- MSVC 9.0(2008) / 10.0(2010) / 11.0(2012u4) / 12.0(2013u4) / 14.0(2015 Preview)  
- GCC 4.9.2 C++03 / C++11 / C++1y  
- LLVM Clang 3.5.0 C++03 / C++11 / C++1y  
  
Note: (Re)generating preprocessed file is required.  
  
Blocks: #48

---

## Comment 1

> Username: Flast  
> Created_at: 2015-02-02 13:39:51 UTC  
> Url: https://github.com/boostorg/fusion/pull/49#issuecomment-72459001  

Minor update for fixing [size / msvc-10.0](http://www.boost.org/development/tests/develop/developer/output/teeks99-08c-win2012R2-64on64-boost-bin-v2-libs-fusion-test-size-test-msvc-10-0-debug-threading-multi.html).

---
