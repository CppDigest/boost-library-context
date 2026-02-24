# #831 refactor: unsafe encoding assumes default options  [Merged]

> Username: alandefreitas  
> Created at: 2024-03-13 01:00:57 UTC  
> Updated at: 2024-03-13 22:13:34 UTC  
> Merged at: 2024-03-13 22:13:34 UTC  
> Closed at: 2024-03-13 22:13:34 UTC  
> Url: https://github.com/boostorg/url/pull/831  

The private unsafe encoding functions received a parameter to determine the encoding options. However, these functions are never used in any context where these options should diverge from the default values.  
  
This is a partial solution to https://github.com/boostorg/url/issues/828, where `detail/encode.hpp` is the file with the lowest coverage.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-03-13 01:21:13 UTC  
> Url: https://github.com/boostorg/url/pull/831#issuecomment-1992957923  

GCOVR code coverage report [https://831.url.prtest2.cppalliance.org/gcovr/index.html](https://831.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://831.url.prtest2.cppalliance.org/genhtml/index.html](https://831.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://831.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://831.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-03-13 01:26:39 UTC  
> Updated_at: 2024-03-13 22:03:16 UTC  
> Url: https://github.com/boostorg/url/pull/831#issuecomment-1992982338  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/831?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.59%. Comparing base [(`6ec189b`)](https://app.codecov.io/gh/boostorg/url/commit/6ec189b931dd6c10045b67ae3cb3710de8301cd5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`a1a5760`)](https://app.codecov.io/gh/boostorg/url/pull/831?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/831/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/831?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #831      +/-   ##  
===========================================  
+ Coverage    98.27%   98.59%   +0.31%       
===========================================  
  Files          157      157                
  Lines         8513     8453      -60       
===========================================  
- Hits          8366     8334      -32       
+ Misses         147      119      -28       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/url/pull/831?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/encode.hpp](https://app.codecov.io/gh/boostorg/url/pull/831?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2VuY29kZS5ocHA=) | `100.00% <100.00%> (+35.89%)` | :arrow_up: |  
| [src/detail/any\_params\_iter.cpp](https://app.codecov.io/gh/boostorg/url/pull/831?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9hbnlfcGFyYW1zX2l0ZXIuY3Bw) | `99.40% <100.00%> (-0.05%)` | :arrow_down: |  
| [src/detail/any\_segments\_iter.cpp](https://app.codecov.io/gh/boostorg/url/pull/831?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9hbnlfc2VnbWVudHNfaXRlci5jcHA=) | `100.00% <ø> (ø)` | |  
| [src/url\_base.cpp](https://app.codecov.io/gh/boostorg/url/pull/831?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3VybF9iYXNlLmNwcA==) | `99.63% <100.00%> (-0.01%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/831?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/831?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6ec189b...a1a5760](https://app.codecov.io/gh/boostorg/url/pull/831?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-03-13 01:37:19 UTC  
> Url: https://github.com/boostorg/url/pull/831#issuecomment-1993029484  

GCOVR code coverage report [https://831.url.prtest2.cppalliance.org/gcovr/index.html](https://831.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://831.url.prtest2.cppalliance.org/genhtml/index.html](https://831.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://831.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://831.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-03-13 15:46:59 UTC  
> Url: https://github.com/boostorg/url/pull/831#issuecomment-1994729971  

GCOVR code coverage report [https://831.url.prtest2.cppalliance.org/gcovr/index.html](https://831.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://831.url.prtest2.cppalliance.org/genhtml/index.html](https://831.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://831.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://831.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-03-13 16:03:21 UTC  
> Url: https://github.com/boostorg/url/pull/831#issuecomment-1994796122  

GCOVR code coverage report [https://831.url.prtest2.cppalliance.org/gcovr/index.html](https://831.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://831.url.prtest2.cppalliance.org/genhtml/index.html](https://831.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://831.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://831.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-03-13 19:41:13 UTC  
> Url: https://github.com/boostorg/url/pull/831#issuecomment-1995535918  

GCOVR code coverage report [https://831.url.prtest2.cppalliance.org/gcovr/index.html](https://831.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://831.url.prtest2.cppalliance.org/genhtml/index.html](https://831.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://831.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://831.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
