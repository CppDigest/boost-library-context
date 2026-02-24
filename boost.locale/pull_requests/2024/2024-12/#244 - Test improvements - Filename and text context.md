# #244 Test improvements - Filename and text context [Merged]

> Username: Flamefire  
> Created at: 2024-12-01 17:30:59 UTC  
> Updated at: 2024-12-02 20:04:54 UTC  
> Merged at: 2024-12-02 16:37:41 UTC  
> Closed at: 2024-12-02 16:37:41 UTC  
> Url: https://github.com/boostorg/locale/pull/244  

To better determine the position of a failed test print the filename in the error message and add support for context-messages:  
  
The first is useful for tests in commonly used headers, the latter in loops over different test inputs

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-12-02 16:37:46 UTC  
> Updated_at: 2024-12-02 16:37:50 UTC  
> Url: https://github.com/boostorg/locale/pull/244#issuecomment-2512079992  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/244?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.81%. Comparing base [(`69e0e30`)](https://app.codecov.io/gh/boostorg/locale/commit/69e0e30d062177ed479a2e97df7a69bdfb0453ed?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`f0d95b0`)](https://app.codecov.io/gh/boostorg/locale/commit/f0d95b079b4be457327350cddd35e76ef3ff4553?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/244/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/244?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #244   +/-   ##  
========================================  
  Coverage    95.81%   95.81%             
========================================  
  Files          117      117             
  Lines        10352    10352             
========================================  
  Hits          9919     9919             
  Misses         433      433             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/locale/pull/244?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_catalog.cpp](https://app.codecov.io/gh/boostorg/locale/pull/244?src=pr&el=tree&filepath=test%2Ftest_catalog.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NhdGFsb2cuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_codecvt.cpp](https://app.codecov.io/gh/boostorg/locale/pull/244?src=pr&el=tree&filepath=test%2Ftest_codecvt.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvZGVjdnQuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_date\_time.cpp](https://app.codecov.io/gh/boostorg/locale/pull/244?src=pr&el=tree&filepath=test%2Ftest_date_time.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2RhdGVfdGltZS5jcHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_encoding.cpp](https://app.codecov.io/gh/boostorg/locale/pull/244?src=pr&el=tree&filepath=test%2Ftest_encoding.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2VuY29kaW5nLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/244?src=pr&el=tree&filepath=test%2Ftest_formatting.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zvcm1hdHRpbmcuY3Bw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/244?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/244?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [69e0e30...f0d95b0](https://app.codecov.io/gh/boostorg/locale/pull/244?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
