# #263 Fix: allow geometry test common to be included in all unit tests [Merged]

> Username: mkaravel  
> Created at: 2015-03-11 18:31:20 UTC  
> Updated at: 2015-03-12 11:52:36 UTC  
> Merged at: 2015-03-11 18:48:56 UTC  
> Closed at: 2015-03-11 18:48:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/263  

In this PR I guard the inclusion of Unit.Test related include by the macro `BOOST_TEST_MODULE`.  
This macro is defined in many unit tests (distance, set-ops, validity, simplicity and possibly others), and is used for using Boost.Test is a different way than in traditionally written unit tests.  
  
When `<geometry_test_common.hpp>` is included in those tests, the `test_main()` function is defined twice and gives a compilation error.  
  
With the guarding proposed in this PR, `<geometry_test_common.hpp>` can be safely included in those unit tests as well, without any problems.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-03-11 18:46:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/263#issuecomment-78343640  

I'm OK

---

## Comment 2

> Username: sdebionne  
> Created_at: 2015-03-12 10:46:02 UTC  
> Updated_at: 2015-03-12 10:48:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/263#issuecomment-78457783  

IMHO, it would have been better to keep the inclusion of Unit.Test headers centralized in `<geometry_test_common.hpp>`.  
  
What about:  
  
``` cpp  
#ifdef BOOST_TEST_MODULE  
# include <boost/test/included/unit_test.hpp>  
#else  
# include <boost/test/included/test_exec_monitor.hpp>  
//#  include <boost/test/included/prg_exec_monitor.hpp>  
# include <boost/test/impl/execution_monitor.ipp>  
#endif  
```  
  
BOOST_TEST_MODULE would mean "use the UTF", the default being "use the test execution monitor". If sometime in the future you want to switch to the linked version of UTF, it will be much easier (but I don't want to add fuel to the fire)...

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-03-12 11:11:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/263#issuecomment-78461091  

The contents of `geometry_test_common.hpp` are quite neutral and generic so, I am not against this idea.  
  
On the other hand this enforces the inclusion of `geometry_test_common.hpp` in all unit tests, and I am not sure this is something we want to enforce.  
  
Let's wait for other opinions about this. If we agree that this is the way we want to go, I can modify the tests that need to be changed, and include this header file.

---

## Comment 4

> Username: sdebionne  
> Created_at: 2015-03-12 11:30:13 UTC  
> Updated_at: 2015-03-12 11:52:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/263#issuecomment-78463495  

The big picture:  
- there are few test suites that are not build with Boost.Test at all (point_concept, robustness);  
- some tests (distance, iterators, strategies, math_sqrt) use UTF and include `unit_test.hpp` directly;  
- very few test suites (util_variant, io_wkt) use TEM and include `test_exec_monitor.hpp` directly;  
- all the rest use TEM and do include `geometry_test_common.hpp`.

---
