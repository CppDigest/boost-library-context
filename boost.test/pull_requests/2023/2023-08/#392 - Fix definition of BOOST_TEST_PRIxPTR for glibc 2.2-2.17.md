# #392 Fix definition of BOOST_TEST_PRIxPTR for glibc 2.2-2.17 [Merged]

> Username: mborland  
> Created at: 2023-08-03 14:31:05 UTC  
> Updated at: 2023-08-04 13:14:41 UTC  
> Merged at: 2023-08-04 13:14:38 UTC  
> Closed at: 2023-08-04 13:14:38 UTC  
> Url: https://github.com/boostorg/test/pull/392  

Closes: #391   
  
glibc 2.2 - 2.17 required `__STDC_FORMAT_MACROS` to be defined for use of `PRIxPTR`. If necessary define and then undef after `<inttypes.h>` is included.

---
