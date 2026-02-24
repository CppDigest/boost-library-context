# #1323 Make SYCL device support opt-in for us too [Merged]

> Username: mborland  
> Created at: 2025-09-26 06:09:27 UTC  
> Updated at: 2025-09-26 16:55:01 UTC  
> Merged at: 2025-09-26 13:11:33 UTC  
> Closed at: 2025-09-26 13:11:33 UTC  
> Url: https://github.com/boostorg/math/pull/1323  

Closes: #1322

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-09-26 08:22:28 UTC  
> Updated_at: 2025-09-26 16:55:01 UTC  
> Url: https://github.com/boostorg/math/pull/1323#issuecomment-3337330102  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1323?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.24%. Comparing base ([`2697069`](https://app.codecov.io/gh/boostorg/math/commit/26970693298662419eb109c8c975dcdbd654da5a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`abcd08c`](https://app.codecov.io/gh/boostorg/math/commit/abcd08cdbf9b7ca881cd6a79627f7bd1090514d5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 7 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1323/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1323?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1323   +/-   ##  
========================================  
  Coverage    95.24%   95.24%             
========================================  
  Files          814      814             
  Lines        69309    69309             
========================================  
  Hits         66015    66015             
  Misses        3294     3294             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1323?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/gegenbauer\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1323?src=pr&el=tree&filepath=test%2Fgegenbauer_test.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9nZWdlbmJhdWVyX3Rlc3QuY3Bw) | `100.00% <ø> (ø)` | |  
| [test/pow\_test.cpp](https://app.codecov.io/gh/boostorg/math/pull/1323?src=pr&el=tree&filepath=test%2Fpow_test.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9wb3dfdGVzdC5jcHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_airy.cpp](https://app.codecov.io/gh/boostorg/math/pull/1323?src=pr&el=tree&filepath=test%2Ftest_airy.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2FpcnkuY3Bw) | `94.44% <ø> (ø)` | |  
| [test/test\_arcsine.cpp](https://app.codecov.io/gh/boostorg/math/pull/1323?src=pr&el=tree&filepath=test%2Ftest_arcsine.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2FyY3NpbmUuY3Bw) | `99.17% <ø> (ø)` | |  
| [test/test\_bernoulli.cpp](https://app.codecov.io/gh/boostorg/math/pull/1323?src=pr&el=tree&filepath=test%2Ftest_bernoulli.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Jlcm5vdWxsaS5jcHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_bessel\_i.cpp](https://app.codecov.io/gh/boostorg/math/pull/1323?src=pr&el=tree&filepath=test%2Ftest_bessel_i.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Jlc3NlbF9pLmNwcA==) | `95.00% <ø> (ø)` | |  
| [test/test\_bessel\_i.hpp](https://app.codecov.io/gh/boostorg/math/pull/1323?src=pr&el=tree&filepath=test%2Ftest_bessel_i.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Jlc3NlbF9pLmhwcA==) | `100.00% <ø> (ø)` | |  
| [test/test\_bessel\_j.cpp](https://app.codecov.io/gh/boostorg/math/pull/1323?src=pr&el=tree&filepath=test%2Ftest_bessel_j.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Jlc3NlbF9qLmNwcA==) | `97.36% <ø> (ø)` | |  
| [test/test\_bessel\_j.hpp](https://app.codecov.io/gh/boostorg/math/pull/1323?src=pr&el=tree&filepath=test%2Ftest_bessel_j.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Jlc3NlbF9qLmhwcA==) | `100.00% <ø> (ø)` | |  
| [test/test\_bessel\_k.cpp](https://app.codecov.io/gh/boostorg/math/pull/1323?src=pr&el=tree&filepath=test%2Ftest_bessel_k.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Jlc3NlbF9rLmNwcA==) | `94.11% <ø> (ø)` | |  
| ... and [54 more](https://app.codecov.io/gh/boostorg/math/pull/1323?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1323?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1323?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2697069...abcd08c](https://app.codecov.io/gh/boostorg/math/pull/1323?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: will-saunders-ukaea  
> Created_at: 2025-09-26 09:50:46 UTC  
> Updated_at: 2025-09-26 09:51:15 UTC  
> Url: https://github.com/boostorg/math/pull/1323#issuecomment-3337768005  

This seems to fix our downstream compilation issues. Many thanks.

---
