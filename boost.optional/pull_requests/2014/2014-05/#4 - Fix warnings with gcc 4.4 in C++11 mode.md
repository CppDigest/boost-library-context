# #4 Fix warnings with gcc 4.4 in C++11 mode [Merged]

> Username: Lastique  
> Created at: 2014-05-22 06:15:39 UTC  
> Updated at: 2014-05-23 06:48:00 UTC  
> Merged at: 2014-05-22 21:38:21 UTC  
> Closed at: 2014-05-22 21:38:21 UTC  
> Url: https://github.com/boostorg/optional/pull/4  

GCC 4.4 has support for an early draft of rvalue references. When compiling the conforming code it produces warnings such as '../boost/optional/optional.hpp:152: warning: returning reference to temporary'. In order to fix this regression use a special implementation of move(), similar to std::move() on this compiler.

---

## Comment 1

> Username: Lastique  
> Created_at: 2014-05-22 06:16:36 UTC  
> Url: https://github.com/boostorg/optional/pull/4#issuecomment-43852175  

The full compiler log can be seen here, for example:  
  
http://www.boost.org/development/tests/develop/developer/output/NA-QNX650-SP1-ARM-experimental-boost-bin-v2-libs-atomic-test-ordering-test-qcc-4-4-2_arm-debug-debug-symbols-off-link-static-threading-multi.html  
  
qcc is just a wrapper around gcc.

---
