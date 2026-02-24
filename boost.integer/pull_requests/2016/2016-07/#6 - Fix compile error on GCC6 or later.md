# #6 Fix compile error on GCC6 or later. [Merged]

> Username: Flast  
> Created at: 2016-07-30 07:22:59 UTC  
> Updated at: 2017-03-31 00:42:36 UTC  
> Merged at: 2017-03-30 18:07:40 UTC  
> Closed at: 2017-03-30 18:07:40 UTC  
> Url: https://github.com/boostorg/integer/pull/6  

Bitwise not yields integral promotion and to be signed type.  
  
This PR will fix [Flast-FreeBSD10-gcc-6.1.1~gnu++11 - integer - integer_mask_include_test / gcc-6.1.1](http://www.boost.org/development/tests/develop/developer/output/Flast-FreeBSD10-gcc-6-1-1~gnu++11-boost-bin-v2-libs-integer-test-integer_mask_include_test-test-gcc-6-1-1-debug.html) and many other failure.  
  
Tested on: GCC 6.1.1-20160711(gnu++98,11,14) and clang 3.8.0(gnu++98,11,14).

---
