# #19 Move cstdint into Boost.Config [Merged]

> Username: glenfe  
> Created at: 2014-06-01 10:11:33 UTC  
> Updated at: 2014-08-01 19:04:04 UTC  
> Merged at: 2014-06-01 17:00:59 UTC  
> Closed at: 2014-06-01 17:00:59 UTC  
> Url: https://github.com/boostorg/config/pull/19  

Retains all history for include/boost/cstdint.hpp

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-06-01 11:07:52 UTC  
> Url: https://github.com/boostorg/config/pull/19#issuecomment-44774997  

Am I correct in thinking this merges all of Config develop to master?  I ask because there are some failures: http://www.boost.org/development/tests/develop/developer/output/BP%20x86_64%20C++11-boost-bin-v2-libs-config-test-config_test-test-clang-linux-3-5~c14_libc++-debug.html

---

## Comment 2

> Username: glenfe  
> Created_at: 2014-06-01 11:17:36 UTC  
> Url: https://github.com/boostorg/config/pull/19#issuecomment-44775186  

Yes: I merged config develop to master before I started on the cstdint move. I can re-do this without that initial merge, but it is trickier if they aren't reconciled.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2014-06-01 11:41:29 UTC  
> Url: https://github.com/boostorg/config/pull/19#issuecomment-44775621  

Are you able to test with latest Clang there and fix the failure as well?  Looks like it might be a bug in the test case?  If so that might be an easier option as we do need to merge to release anyway at some point.

---

## Comment 4

> Username: glenfe  
> Created_at: 2014-06-01 15:46:46 UTC  
> Url: https://github.com/boostorg/config/pull/19#issuecomment-44780780  

Sure, I'll take care of that now.

---

## Comment 5

> Username: glenfe  
> Created_at: 2014-06-01 16:24:35 UTC  
> Url: https://github.com/boostorg/config/pull/19#issuecomment-44781729  

Done.

---
