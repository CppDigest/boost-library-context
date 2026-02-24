# #252 Made BOOST_CHARCONV_HAS_QUADMATH a usage requirement in the Jamfile [Merged]

> Username: anarthal  
> Created at: 2025-01-03 14:50:11 UTC  
> Updated at: 2025-01-03 18:23:34 UTC  
> Merged at: 2025-01-03 18:18:24 UTC  
> Closed at: 2025-01-03 18:18:24 UTC  
> Url: https://github.com/boostorg/charconv/pull/252  

Removed unnecessary BOOST_CHARCONV_NO_QUADMATH macro definition from CMake  
Changed all BOOST_CHARCONV_HAS_FLOAT128 usages into BOOST_CHARCONV_HAS_QUADMATH, which is guaranteed to be correctly propagated  
Fixed tests that had missing includes, unnoticed due to being ifdef-ed out by BOOST_CHARCONV_HAS_QUADMATH  
  
close #249

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-01-03 14:55:12 UTC  
> Url: https://github.com/boostorg/charconv/pull/252#issuecomment-2569353659  

* Note that the `BOOST_CHARCONV_NO_QUADMATH` CMake option is still valid and working. I've only removed the C++ macro definition it caused, as it's not used and doesn't have an equivalent in the Jamfile.  
* `BOOST_CHARCONV_HAS_FLOAT128` was defined in the sources only if `BOOST_CHARCONV_HAS_QUADMATH`, but was then used in header files in the public API, where the macro had no chance of being defined. This led to some of the `__float128` overloads being incorrecly ifdef-ed out, even when quadmath is available.  
* Since `BOOST_CHARCONV_HAS_QUADMATH` was not being propagated in the Jamfile, some tests were not being run and had missing includes. I think I got them right, but it'd be nice if you could review them.  
* I've removed the requirement to link to quadmath in the test Jamfile, since this is now handled by the usage requirement. Note that this also causes the tests to run from CMake, since `boost_test_jamfile` only picks simple test lines.

---

## Comment 2

> Username: mborland  
> Created_at: 2025-01-03 15:08:03 UTC  
> Url: https://github.com/boostorg/charconv/pull/252#issuecomment-2569371503  

> * Note that the `BOOST_CHARCONV_NO_QUADMATH` CMake option is still valid and working. I've only removed the C++ macro definition it caused, as it's not used and doesn't have an equivalent in the Jamfile.  
> * `BOOST_CHARCONV_HAS_FLOAT128` was defined in the sources only if `BOOST_CHARCONV_HAS_QUADMATH`, but was then used in header files in the public API, where the macro had no chance of being defined. This led to some of the `__float128` overloads being incorrecly ifdef-ed out, even when quadmath is available.  
> * Since `BOOST_CHARCONV_HAS_QUADMATH` was not being propagated in the Jamfile, some tests were not being run and had missing includes. I think I got them right, but it'd be nice if you could review them.  
> * I've removed the requirement to link to quadmath in the test Jamfile, since this is now handled by the usage requirement. Note that this also causes the tests to run from CMake, since `boost_test_jamfile` only picks simple test lines.  
  
Thanks for the run down. Your part looks good; I'll come behind and clean up newly exposed warnings/errors.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2025-01-03 17:59:15 UTC  
> Updated_at: 2025-01-03 18:23:34 UTC  
> Url: https://github.com/boostorg/charconv/pull/252#issuecomment-2569612953  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/252?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.85%. Comparing base [(`ffd3e71`)](https://app.codecov.io/gh/boostorg/charconv/commit/ffd3e710d07e08720f88357ab008baf1a4fdb7a3?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`0cd42b7`)](https://app.codecov.io/gh/boostorg/charconv/commit/0cd42b71315b5abbe6040997f5e20954dd9daac2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 13 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/252/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/252?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #252   +/-   ##  
========================================  
  Coverage    94.85%   94.85%             
========================================  
  Files           69       69             
  Lines         9085     9085             
========================================  
  Hits          8618     8618             
  Misses         467      467             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/charconv/pull/252?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/charconv/detail/buffer\_sizing.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/252?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fbuffer_sizing.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvYnVmZmVyX3NpemluZy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/charconv/detail/issignaling.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/252?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fissignaling.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvaXNzaWduYWxpbmcuaHBw) | `100.00% <ø> (ø)` | |  
| [...lude/boost/charconv/detail/ryu/ryu\_generic\_128.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/252?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fryu%2Fryu_generic_128.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvcnl1L3J5dV9nZW5lcmljXzEyOC5ocHA=) | `92.72% <100.00%> (ø)` | |  
| [include/boost/charconv/from\_chars.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/252?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Ffrom_chars.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9mcm9tX2NoYXJzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [src/to\_chars\_float\_impl.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/252?src=pr&el=tree&filepath=src%2Fto_chars_float_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3RvX2NoYXJzX2Zsb2F0X2ltcGwuaHBw) | `83.39% <ø> (ø)` | |  
| [test/github\_issue\_110.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/252?src=pr&el=tree&filepath=test%2Fgithub_issue_110.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMTEwLmNwcA==) | `100.00% <ø> (ø)` | |  
| [test/github\_issue\_152\_float128.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/252?src=pr&el=tree&filepath=test%2Fgithub_issue_152_float128.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMTUyX2Zsb2F0MTI4LmNwcA==) | `100.00% <ø> (ø)` | |  
| [test/test\_128bit\_emulation.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/252?src=pr&el=tree&filepath=test%2Ftest_128bit_emulation.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0XzEyOGJpdF9lbXVsYXRpb24uY3Bw) | `100.00% <ø> (ø)` | |  
| [test/test\_float128.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/252?src=pr&el=tree&filepath=test%2Ftest_float128.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zsb2F0MTI4LmNwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/252?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/252?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ffd3e71...0cd42b7](https://app.codecov.io/gh/boostorg/charconv/pull/252?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
