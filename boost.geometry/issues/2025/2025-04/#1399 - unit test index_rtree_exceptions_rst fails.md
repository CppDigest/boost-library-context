# #1399 - unit test index_rtree_exceptions_rst fails [Open]

> Username: barendgehrels  
> Created at: 2025-04-26 09:06:32 UTC  
> Updated at: 2025-05-07 06:21:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/1399  

It is currently the only failing test.  
Report:  
```  
99% tests passed, 1 tests failed out of 388  
  
Total Test time (real) = 207.51 sec  
  
The following tests FAILED:  
	370 - boost_geometry_index_rtree_exceptions_rst (Failed)  
  
388/388 Test 370: boost_geometry_index_rtree_exceptions_rst ...................***Failed    7.75 sec  
```  
  
Details:  
  
```  
$ ./index/test/rtree/exceptions/boost_geometry_index_rtree_exceptions_rst  
Running 1 test case...  
.../boost/libs/geometry/index/test/rtree/exceptions/test_exceptions.hpp:53: error: in "test_main_caller( argc_ argv )": check bgi::detail::rtree::utilities::are_counts_ok(tree, false) has failed  
.../boost/libs/geometry/index/test/rtree/exceptions/test_exceptions.hpp:53: error: in "test_main_caller( argc_ argv )": check bgi::detail::rtree::utilities::are_counts_ok(tree, false) has failed  
.../boost/libs/geometry/index/test/rtree/exceptions/test_exceptions.hpp:53: error: in "test_main_caller( argc_ argv )": check bgi::detail::rtree::utilities::are_counts_ok(tree, false) has failed  
.../boost/libs/geometry/index/test/rtree/exceptions/test_exceptions.hpp:53: error: in "test_main_caller( argc_ argv )": check bgi::detail::rtree::utilities::are_counts_ok(tree, false) has failed  
.../boost/libs/geometry/index/test/rtree/exceptions/test_exceptions.hpp:53: error: in "test_main_caller( argc_ argv )": check bgi::detail::rtree::utilities::are_counts_ok(tree, false) has failed  
.../boost/libs/geometry/index/test/rtree/exceptions/test_exceptions.hpp:53: error: in "test_main_caller( argc_ argv )": check bgi::detail::rtree::utilities::are_counts_ok(tree, false) has failed  
.../boost/libs/geometry/index/test/rtree/exceptions/test_exceptions.hpp:53: error: in "test_main_caller( argc_ argv )": check bgi::detail::rtree::utilities::are_counts_ok(tree, false) has failed  
  
*** 7 failures are detected in the test module "Test Program"  
  
```

---

## Comment 1

> Username: awulkiew  
> Created at: 2025-05-05 21:54:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/1399#issuecomment-2852425402  

I'll look at it. Any idea what caused it? https://github.com/boostorg/geometry/pull/1391, https://github.com/boostorg/geometry/pull/1392 or something else?

---

## Comment 2

> Username: awulkiew  
> Created at: 2025-05-06 00:12:11 UTC  
> Updated at: 2025-05-06 00:13:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/1399#issuecomment-2852809020  

Where does it fail?  
It seems to be fine: https://www.boost.org/development/tests/develop/developer/geometry-index.html  
It's not tested on CircleCI. Are you running it locally or am I missing something?

---

## Comment 3

> Username: barendgehrels  
> Created at: 2025-05-06 06:05:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/1399#issuecomment-2853374590  

I'm running it locally... apart from `are_counts_ok(tree, false)` I don't have more info (yet)

---

## Comment 4

> Username: barendgehrels  
> Created at: 2025-05-06 06:06:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/1399#issuecomment-2853375590  

It's the only test failing. If it is only locally I'm also fine excluding it from cmake. But it's still weird.

---

## Comment 5

> Username: barendgehrels  
> Created at: 2025-05-06 17:47:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/1399#issuecomment-2855427259  

If I change the code from  
```  
BOOST_CHECK(bgi::detail::rtree::utilities::are_counts_ok(tree, false));  
```  
  
into:  
```  
BOOST_CHECK_MESSAGE(bgi::detail::rtree::utilities::are_counts_ok(tree, false),  
            "are_counts_ok failed"  
            << " at i = " << i  
            << " with max_calls = " << throwing_value::get_max_calls_ref());  
```  
  
I get the next output:  
```  
Running 1 test case...  
.../boost/libs/geometry/index/test/rtree/exceptions/test_exceptions.hpp:56: error: in "test_main_caller( argc_ argv )": are_counts_ok failed at i = 34 with max_calls = 34  
.../boost/libs/geometry/index/test/rtree/exceptions/test_exceptions.hpp:56: error: in "test_main_caller( argc_ argv )": are_counts_ok failed at i = 36 with max_calls = 36  
.../boost/libs/geometry/index/test/rtree/exceptions/test_exceptions.hpp:56: error: in "test_main_caller( argc_ argv )": are_counts_ok failed at i = 38 with max_calls = 38  
.../boost/libs/geometry/index/test/rtree/exceptions/test_exceptions.hpp:56: error: in "test_main_caller( argc_ argv )": are_counts_ok failed at i = 40 with max_calls = 40  
.../boost/libs/geometry/index/test/rtree/exceptions/test_exceptions.hpp:56: error: in "test_main_caller( argc_ argv )": are_counts_ok failed at i = 42 with max_calls = 42  
.../boost/libs/geometry/index/test/rtree/exceptions/test_exceptions.hpp:56: error: in "test_main_caller( argc_ argv )": are_counts_ok failed at i = 44 with max_calls = 44  
.../boost/libs/geometry/index/test/rtree/exceptions/test_exceptions.hpp:56: error: in "test_main_caller( argc_ argv )": are_counts_ok failed at i = 46 with max_calls = 46  
  
*** 7 failures are detected in the test module "Test Program"  
  
```  
  
If I comment the `BOOST_CHECK_THROW` line, the test passes.  
  
I don't understand the test, and I don't understand the failure, so it's up to you...

---

## Comment 6

> Username: awulkiew  
> Created at: 2025-05-06 23:29:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/1399#issuecomment-2856496701  

Sure, no problem, thanks for the details. I'd like to try to reproduce it. What's the OS and compiler?

---

## Comment 7

> Username: awulkiew  
> Created at: 2025-05-06 23:31:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/1399#issuecomment-2856516864  

I'd probably also need cmake options.

---

## Comment 8

> Username: barendgehrels  
> Created at: 2025-05-07 06:21:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/1399#issuecomment-2857244565  

> Sure, no problem, thanks for the details. I'd like to try to reproduce it. What's the OS and compiler?  
  
I've:  
```  
Apple clang version 15.0.0 (clang-1500.1.0.2.5)  
Target: arm64-apple-darwin23.6.0  
  
Darwin MAC-CJ6XM46W09 23.6.0 Darwin Kernel Version 23.6.0: Thu Mar  6 21:58:56 PST 2025; root:xnu-10063.141.1.704.6~1/RELEASE_ARM64_T6020 arm64  
```  
  
For clang I don't set extra options.
