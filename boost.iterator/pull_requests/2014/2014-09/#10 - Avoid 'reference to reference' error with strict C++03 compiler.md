# #10 Avoid 'reference to reference' error with strict C++03 compiler. [Merged]

> Username: Flast  
> Created at: 2014-09-29 06:57:59 UTC  
> Updated at: 2014-11-03 05:11:47 UTC  
> Merged at: 2014-11-02 22:59:02 UTC  
> Closed at: 2014-11-02 22:59:02 UTC  
> Url: https://github.com/boostorg/iterator/pull/10  

This change will fix Range's tests; [DebSidC++ - range - join / gcc-4.9.1~98](http://www.boost.org/development/tests/develop/developer/output/DebSidC++-boost-bin-v2-libs-range-test-join-test-gcc-4-9-1~98-debug-link-static-threading-multi.html).  
  
Tested under, x86-64 linux with  
- GCC 4.8.3(gnu++03,c++03,gnu++11,c++11,gnu++1y,c++1y)  
- Clang 3.4(gnu++03,c++03,gnu++11,c++11,gnu++1y,c++1y).  
  
Also, it might fix similar error on older msvc (I didn't test, because I have no such msvc...).  
- [VC8 jc-bell - range - join / msvc-8.0](http://www.boost.org/development/tests/develop/developer/output/VC8%20jc-bell-boost-bin-v2-libs-range-test-join-test-msvc-8-0-debug-link-static-threading-multi.html)  
- [teeks99-08a-win2012R2-64on64 - range - join / msvc-8.0](http://www.boost.org/development/tests/develop/developer/output/teeks99-08a-win2012R2-64on64-boost-bin-v2-libs-range-test-join-test-msvc-8-0-debug-link-static-threading-multi.html)  
- [teeks99-08b-win2012R2-64on64 - range - join / msvc-9.0](http://www.boost.org/development/tests/develop/developer/output/teeks99-08b-win2012R2-64on64-boost-bin-v2-libs-range-test-join-test-msvc-9-0-debug-link-static-threading-multi.html)  
- [teeks99-test - range - join / msvc-8.0](http://www.boost.org/development/tests/develop/developer/output/teeks99-test-boost-bin-v2-libs-range-test-join-test-msvc-8-0-debug-address-model-64-link-static-threading-multi.html)

---
