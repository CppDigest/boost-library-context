# #14 Do not use std::auto_ptr. [Merged]

> Username: Flast  
> Created at: 2016-07-30 08:24:09 UTC  
> Updated at: 2016-07-31 06:57:07 UTC  
> Merged at: 2016-07-30 18:08:11 UTC  
> Closed at: 2016-07-30 18:08:11 UTC  
> Url: https://github.com/boostorg/pool/pull/14  

Use boost::scoped_ptr instead in this use case.  
  
see [Flast-FreeBSD10-gcc-5.3.0~gnu++11 - pool - test_bug_5526 / gcc-5.3.0](http://www.boost.org/development/tests/develop/developer/output/Flast-FreeBSD10-gcc-5-3-0~gnu++11-boost-bin-v2-libs-pool-test-test_bug_5526-test-gcc-5-3-0-debug.html)

---
