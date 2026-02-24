# #241 Fix parsing of numbers in floating point format to integers [Merged]

> Username: Flamefire  
> Created at: 2024-11-10 19:00:52 UTC  
> Updated at: 2024-11-17 12:23:02 UTC  
> Merged at: 2024-11-17 12:22:55 UTC  
> Closed at: 2024-11-17 12:22:55 UTC  
> Url: https://github.com/boostorg/locale/pull/241  

When parsing a string like "123.456" to an integer the ICU backend would first parse it greedily to a floating point value and then cast/truncate it to an integer.  
Set the flag to only parse integers when parsing to an integral number. Care must be taken not to set that when parsing e.g. a currency or date to an integer where the truncation is intended.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-11-12 23:46:32 UTC  
> Updated_at: 2024-11-17 12:23:02 UTC  
> Url: https://github.com/boostorg/locale/pull/241#issuecomment-2471872042  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/241?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.81%. Comparing base [(`ff91706`)](https://app.codecov.io/gh/boostorg/locale/commit/ff917062e50b7d789a58279bf2416037486d5901?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`1e9e855`)](https://app.codecov.io/gh/boostorg/locale/commit/1e9e85546ed0811b87a374a6e363b6b20c633a42?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 4 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/241/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/241?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #241      +/-   ##  
===========================================  
+ Coverage    95.76%   95.81%   +0.05%       
===========================================  
  Files          116      117       +1       
  Lines        10035    10353     +318       
===========================================  
+ Hits          9610     9920     +310       
- Misses         425      433       +8       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/locale/pull/241?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/boost/locale/icu/formatter.cpp](https://app.codecov.io/gh/boostorg/locale/pull/241?src=pr&el=tree&filepath=src%2Fboost%2Flocale%2Ficu%2Fformatter.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZm9ybWF0dGVyLmNwcA==) | `79.02% <100.00%> (+0.31%)` | :arrow_up: |  
| [test/formatting\_common.hpp](https://app.codecov.io/gh/boostorg/locale/pull/241?src=pr&el=tree&filepath=test%2Fformatting_common.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9mb3JtYXR0aW5nX2NvbW1vbi5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [test/test\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/241?src=pr&el=tree&filepath=test%2Ftest_formatting.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zvcm1hdHRpbmcuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_posix\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/241?src=pr&el=tree&filepath=test%2Ftest_posix_formatting.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3Bvc2l4X2Zvcm1hdHRpbmcuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [test/test\_std\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/241?src=pr&el=tree&filepath=test%2Ftest_std_formatting.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N0ZF9mb3JtYXR0aW5nLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_winapi\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/241?src=pr&el=tree&filepath=test%2Ftest_winapi_formatting.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3dpbmFwaV9mb3JtYXR0aW5nLmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
... and [55 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/locale/pull/241/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/241?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/241?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ff91706...1e9e855](https://app.codecov.io/gh/boostorg/locale/pull/241?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
