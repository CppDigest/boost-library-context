# #218 Undefine BOOST_GCC for intel compilers [Merged]

> Username: danieljames  
> Created at: 2018-03-11 21:20:16 UTC  
> Updated at: 2018-03-16 18:13:59 UTC  
> Merged at: 2018-03-16 18:13:59 UTC  
> Closed at: 2018-03-16 18:13:59 UTC  
> Url: https://github.com/boostorg/config/pull/218  

Also, this doesn't fix it, but apparently intel compilers don't support `__attribute__((fallthrough))` but BOOST_FALLTHROUGH is defined by the gcc config, according to the beast test results:  
  
http://www.boost.org/development/tests/master/developer/output/intel-linux-master-boost-bin-v2-libs-beast-test-beast-websocket-accept-test-intel-linux-linux-debug-threadapi-pthread-threading-multi.html

---
