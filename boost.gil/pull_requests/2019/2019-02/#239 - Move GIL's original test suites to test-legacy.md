# #239 Move GIL's original test suites to test/legacy/ [Merged]

> Username: mloskot  
> Created at: 2019-02-19 23:42:59 UTC  
> Updated at: 2019-03-29 20:27:05 UTC  
> Merged at: 2019-02-20 19:03:29 UTC  
> Closed at: 2019-02-20 19:03:29 UTC  
> Url: https://github.com/boostorg/gil/pull/239  

Document maintenance rules of `test/legacy/` content.  
  
Motivation to move the `test/legacy/` is to:  
* clarify status of those tests  
* make it easier to run tests selectively  
* separate new/upcoming feature-specific tests  
  from those all-in-one tests.  
  
Other changes:  
Rename `gil_test_common.hpp` to `unit_test.hpp` to make  
it clearer it acts as `boost/test/unit_test.hpp proxy`.  
Remove `<include>$(BOOST_ROOT)` from `Jamfile`-s as unnecessary.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---
