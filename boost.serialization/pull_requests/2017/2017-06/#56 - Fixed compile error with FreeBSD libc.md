# #56 Fixed compile error with FreeBSD libc. [Closed]

> Username: Flast  
> Created at: 2017-06-13 09:41:13 UTC  
> Updated at: 2017-07-25 01:11:39 UTC  
> Closed at: 2017-07-25 01:11:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/56  

FreeBSD libc defines NULL as nullptr in c++11 or later mode; nullptr cannot convert to arithmetic type.  
  
see https://svnweb.freebsd.org/base/head/sys/sys/_null.h?r1=192002&r2=228918  
  
This PR will fix many test failure, like [Test output: Flast-FreeBSD11 - serialization - test_iterators / clang-linux-3.8~gnu++11](http://www.boost.org/development/tests/develop/developer/output/Flast-FreeBSD11-boost-bin-v2-libs-serialization-test-test_iterators-test-clang-linux-3-8~gnu++11-debug.html).  
  
Tested on FreeBSD 11.0 with clang 3.8.0.

---

## Comment 1

> Username: jwakely  
> Created_at: 2017-07-24 09:54:38 UTC  
> Url: https://github.com/boostorg/serialization/pull/56#issuecomment-317374777  

This was fixed on the develop branch by 81b44504c5a085265f851d1d682a518046c08f16

---

## Comment 2

> Username: Flast  
> Created_at: 2017-07-25 01:11:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/56#issuecomment-317599089  

Thanks!

---
