# #1254 Replace deprecated runners in CI [Merged]

> Username: mborland  
> Created at: 2025-04-08 17:52:48 UTC  
> Updated at: 2025-04-12 18:06:43 UTC  
> Merged at: 2025-04-12 14:34:10 UTC  
> Closed at: 2025-04-12 14:34:10 UTC  
> Url: https://github.com/boostorg/math/pull/1254  



---

## Comment 1

> Username: mborland  
> Created_at: 2025-04-08 19:22:55 UTC  
> Url: https://github.com/boostorg/math/pull/1254#issuecomment-2787453452  

I'm not quite sure why we have a bloodbath from Boost.Exception now. The file that's popping up as an error hasn't been modified in over 2 years.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2025-04-10 18:07:43 UTC  
> Url: https://github.com/boostorg/math/pull/1254#issuecomment-2794729021  

We must have a test that has <exception-handling>off and is - possibly indirectly - pulling in Boost.Exception.  I don't see it at present though.

---

## Comment 3

> Username: mborland  
> Created_at: 2025-04-10 18:17:30 UTC  
> Url: https://github.com/boostorg/math/pull/1254#issuecomment-2794752971  

I think it's coming up as a secondary dependency from Boost.Container since the failure looks like:  
  
```  
gcc.compile.c++ ../../../bin.v2/libs/container/build/gcc-14/debug/x86_64/exception-handling-off/link-static/threading-multi/visibility-hidden/pool_resource.o  
gcc.compile.c++ ../../../bin.v2/libs/system/build/gcc-14/debug/x86_64/exception-handling-off/link-static/threading-multi/visibility-hidden/error_code.o  
gcc.archive ../../../bin.v2/libs/container/build/gcc-14/debug/x86_64/exception-handling-off/link-static/threading-multi/visibility-hidden/libboost_container.a  
gcc.archive ../../../bin.v2/libs/system/build/gcc-14/debug/x86_64/exception-handling-off/link-static/threading-multi/visibility-hidden/libboost_system.a  
gcc.compile.c++ ../../../bin.v2/libs/exception/build/gcc-14/debug/x86_64/exception-handling-off/link-static/threading-multi/visibility-hidden/clone_current_exception_non_intrusive.o  
In file included from ../../../libs/exception/src/clone_current_exception_non_intrusive.cpp:14:  
../../../boost/exception/detail/clone_current_exception.hpp:22:6: error: #error This header requires exception handling to be enabled.  
   22 | #    error This header requires exception handling to be enabled.  
      |      ^~~~~  
  
    "g++-14"   -fvisibility-inlines-hidden -fno-exceptions -std=c++20 -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_MATH_RUN_MP_TESTS -DCI_SUPPRESS_KNOWN_ISSUES -DSLOW_COMPILER   -I"../../.."  -c -o "../../../bin.v2/libs/exception/build/gcc-14/debug/x86_64/exception-handling-off/link-static/threading-multi/visibility-hidden/clone_current_exception_non_intrusive.o" "../../../libs/exception/src/clone_current_exception_non_intrusive.cpp"  
```  
  
`test_instantiate` has some very old dependencies in it so maybe breaking those would help?

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2025-04-10 18:42:23 UTC  
> Url: https://github.com/boostorg/math/pull/1254#issuecomment-2794819012  

test_instantiate is a compile-only test, it shouldn't be building *any* dependencies IMO.

---

## Comment 5

> Username: mborland  
> Created_at: 2025-04-10 18:49:54 UTC  
> Updated_at: 2025-04-10 18:55:41 UTC  
> Url: https://github.com/boostorg/math/pull/1254#issuecomment-2794845502  

> test_instantiate is a compile-only test, it shouldn't be building _any_ dependencies IMO.  
  
Should this be changed to compile then?  
  
```  
[ run test_instantiate1.cpp test_instantiate2.cpp  : : : <exception-handling>off : test_instantiate_no_eh ]  
```  
  
In the no-exception handling runs everything fails so that only kind of helps?  
  
Edit: Locally this works fine `[ compile test_instantiate1.cpp test_instantiate2.cpp : <exception-handling>off : test_instantiate_no_eh ]`

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2025-04-11 08:56:10 UTC  
> Url: https://github.com/boostorg/math/pull/1254#issuecomment-2796276474  

You're correct: the build log clearly shows that test_instantiate is the culprit, BUT it passes locally for me, and the Jamfile clearly shows no dependencies at all for that target, and certainly no mention of Boost.Exception being required.  For that matter Boost.Exception (the library) is absolutely NOT a dependency, only the stub header.  So I'm confused right now.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2025-04-11 09:03:31 UTC  
> Url: https://github.com/boostorg/math/pull/1254#issuecomment-2796293283  

Reproduced locally.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2025-04-11 17:14:51 UTC  
> Url: https://github.com/boostorg/math/pull/1254#issuecomment-2797528970  

The dependency is introduced by Boost.Test which isn't used by that target, I nearly have a fix that I'm testing locally...

---

## Comment 9

> Username: codecov[bot]  
> Created_at: 2025-04-12 12:23:04 UTC  
> Updated_at: 2025-04-12 18:06:42 UTC  
> Url: https://github.com/boostorg/math/pull/1254#issuecomment-2798811646  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1254?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.83%. Comparing base [(`a5c0625`)](https://app.codecov.io/gh/boostorg/math/commit/a5c062542afb2037054c4e1b1be44db7b0859c38?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`5a41763`)](https://app.codecov.io/gh/boostorg/math/commit/5a41763ea74301a4c9fc6971b4a41c5ffada09cd?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 13 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1254/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1254?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1254   +/-   ##  
========================================  
  Coverage    93.83%   93.83%             
========================================  
  Files          657      657             
  Lines        55244    55244             
========================================  
  Hits         51840    51840             
  Misses        3404     3404             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1254?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/tools/big\_constant.hpp](https://app.codecov.io/gh/boostorg/math/pull/1254?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Ftools%2Fbig_constant.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3Rvb2xzL2JpZ19jb25zdGFudC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/math/tools/test\_value.hpp](https://app.codecov.io/gh/boostorg/math/pull/1254?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Ftools%2Ftest_value.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3Rvb2xzL3Rlc3RfdmFsdWUuaHBw) | `100.00% <ø> (ø)` | |  
| [test/test\_hermite.hpp](https://app.codecov.io/gh/boostorg/math/pull/1254?src=pr&el=tree&filepath=test%2Ftest_hermite.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2hlcm1pdGUuaHBw) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1254?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1254?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a5c0625...5a41763](https://app.codecov.io/gh/boostorg/math/pull/1254?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2025-04-12 14:33:52 UTC  
> Url: https://github.com/boostorg/math/pull/1254#issuecomment-2798854677  

OK this looks good to go, only failures are from cycl Cauchy tests.  
  
I'll let @mborland look at those.

---
