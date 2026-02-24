# #267 Fix cygwin tests [Merged]

> Username: Flamefire  
> Created at: 2025-10-21 08:00:41 UTC  
> Updated at: 2025-10-29 09:03:45 UTC  
> Merged at: 2025-10-29 07:44:15 UTC  
> Closed at: 2025-10-29 07:44:15 UTC  
> Url: https://github.com/boostorg/locale/pull/267  

Improve tests so they pass on Appveyor Cygwin again

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-10-21 13:19:57 UTC  
> Updated_at: 2025-10-29 09:03:45 UTC  
> Url: https://github.com/boostorg/locale/pull/267#issuecomment-3426613072  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/267?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.93%. Comparing base ([`38dda57`](https://app.codecov.io/gh/boostorg/locale/commit/38dda57f107a2b3840b84d7566d00a5983046801?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`2690f0c`](https://app.codecov.io/gh/boostorg/locale/commit/2690f0c363a086c43e6076b50022b759123fbe4b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 16 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/267/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/267?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #267      +/-   ##  
===========================================  
+ Coverage    95.76%   95.93%   +0.17%       
===========================================  
  Files          119      119                
  Lines        10331    10370      +39       
===========================================  
+ Hits          9893     9948      +55       
+ Misses         438      422      -16       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/locale/pull/267?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/generic\_codecvt.hpp](https://app.codecov.io/gh/boostorg/locale/pull/267?src=pr&el=tree&filepath=include%2Fboost%2Flocale%2Fgeneric_codecvt.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZ2VuZXJpY19jb2RlY3Z0LmhwcA==) | `97.57% <100.00%> (ø)` | |  
| [src/std/codecvt.cpp](https://app.codecov.io/gh/boostorg/locale/pull/267?src=pr&el=tree&filepath=src%2Fstd%2Fcodecvt.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3N0ZC9jb2RlY3Z0LmNwcA==) | `100.00% <ø> (ø)` | |  
| [src/std/std\_backend.cpp](https://app.codecov.io/gh/boostorg/locale/pull/267?src=pr&el=tree&filepath=src%2Fstd%2Fstd_backend.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3N0ZC9zdGRfYmFja2VuZC5jcHA=) | `92.45% <100.00%> (+1.36%)` | :arrow_up: |  
| [test/test\_encoding.cpp](https://app.codecov.io/gh/boostorg/locale/pull/267?src=pr&el=tree&filepath=test%2Ftest_encoding.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2VuY29kaW5nLmNwcA==) | `99.31% <100.00%> (+0.02%)` | :arrow_up: |  
| [test/test\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/267?src=pr&el=tree&filepath=test%2Ftest_formatting.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zvcm1hdHRpbmcuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_posix\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/267?src=pr&el=tree&filepath=test%2Ftest_posix_formatting.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3Bvc2l4X2Zvcm1hdHRpbmcuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_std\_convert.cpp](https://app.codecov.io/gh/boostorg/locale/pull/267?src=pr&el=tree&filepath=test%2Ftest_std_convert.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N0ZF9jb252ZXJ0LmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_std\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/267?src=pr&el=tree&filepath=test%2Ftest_std_formatting.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N0ZF9mb3JtYXR0aW5nLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_stream\_io.cpp](https://app.codecov.io/gh/boostorg/locale/pull/267?src=pr&el=tree&filepath=test%2Ftest_stream_io.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N0cmVhbV9pby5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [test/test\_winapi\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/267?src=pr&el=tree&filepath=test%2Ftest_winapi_formatting.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3dpbmFwaV9mb3JtYXR0aW5nLmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
... and [12 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/267/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/267?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/267?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [38dda57...2690f0c](https://app.codecov.io/gh/boostorg/locale/pull/267?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
