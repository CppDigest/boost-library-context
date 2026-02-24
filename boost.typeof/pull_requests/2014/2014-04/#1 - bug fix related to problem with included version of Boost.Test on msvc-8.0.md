# #1 [typeof][msvc-8.0] bug fix related to problem with included version of Boost.Test on msvc-8.0 [Closed]

> Username: mkaravel  
> Created at: 2014-04-20 21:12:05 UTC  
> Updated at: 2021-09-22 06:55:48 UTC  
> Closed at: 2021-09-22 06:55:48 UTC  
> Url: https://github.com/boostorg/typeof/pull/1  

When both boost/utility/enable_if.hpp and boost/test/included/unit_test.hpp are included,  
msvc-8.0 cannot figure out whether the enable_if and disable_if used in te typeof implementation  
for msvc-8.0 is that in the boost namespace or that in the boost::unit_test::decorator namespace.  
  
Proposed solution: explicitly qualify enable_if and disable_if with the boost namespace.  
  
This problem has appeared in recent boost/geometry unit tests, on msvc-8.0 platforms, where the included version of the unit test is used.  
  
For example see:  
http://www.boost.org/development/tests/develop/developer/output/teeks99-02a-win2008-64on64-boost-bin-v2-libs-geometry-test-algorithms-difference_linear_linear-test-msvc-8-0-debug-asynch-exceptions-on-threading-multi.html  
for the results of the unit test, and:  
https://github.com/boostorg/geometry/blob/develop/test/algorithms/difference_linear_linear.cpp  
for the source code of the unit test.

---

## Comment 1

> Username: K-ballo  
> Created_at: 2014-07-03 23:17:14 UTC  
> Url: https://github.com/boostorg/typeof/pull/1#issuecomment-47994711  

Looks good to me

---

## Comment 2

> Username: mclow  
> Created_at: 2015-04-10 14:33:52 UTC  
> Url: https://github.com/boostorg/typeof/pull/1#issuecomment-91576433  

This looks fine to me, too.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2016-06-23 12:25:50 UTC  
> Updated_at: 2016-06-23 12:30:22 UTC  
> Url: https://github.com/boostorg/typeof/pull/1#issuecomment-228034076  

I don't see this issue anymore, possibly due to the changes in Test.  
  
UPDATE: or more probably because Geometry no longer depends on TypeOf.

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2018-06-21 21:49:57 UTC  
> Url: https://github.com/boostorg/typeof/pull/1#issuecomment-399254619  

Close?

---
