# #714 Ada tests [Merged]

> Username: alandefreitas  
> Created at: 2023-03-16 16:36:18 UTC  
> Updated at: 2024-02-21 00:54:16 UTC  
> Merged at: 2023-03-16 19:35:44 UTC  
> Closed at: 2023-03-16 19:35:44 UTC  
> Url: https://github.com/boostorg/url/pull/714  

This PR implements the basic tests from [Ada](https://github.com/ada-url/ada). It replicates the [basic tests](https://github.com/ada-url/ada/blob/main/tests/basic_tests.cpp) and a [second basic test](https://github.com/ada-url/ada/blob/main/tests/url_components.cpp), which is based on [fixtures](https://github.com/ada-url/ada/blob/main/tests/wpt/urltestdata.json). The Ada repository has [8 more files with fixtures](https://github.com/ada-url/ada/tree/main/tests/wpt). I expect we'll have about the same number of conflicts in these files so I'll describe what these conflicts are.  
  
The tests were helpful to find bugs (5 bugs were found over 767 tests). On the other hand, Ada and node.js implement lots of exceptions for corner cases. And their tests are often exploring the very same corner cases we don't have. This means a large number of URLs (473 - 62%) have different expected results in Ada. For instance, modifying URLs in Ada might have different results depending on the scheme or even on whether some components "look like numbers".  
  
So for each case, I had to check if it was really a bug or if the difference was intended behavior. That involved checking the RFC again. Here's a summary of the reasons why the expected results didn't match:  
  
<details>  
  
  
  <summary>Summary Ada and Boost.URL conflicts</summary>  
  
  
- Useful tests: 294 (38.3%)  
    - Boost.URL and Ada expected results are the same: 294 (38.3%)  
- Ada sanitizes URLs: 158 (20.6%)  
    - Ada fixes URLs with invalid chars: 140 (18.3%)  
    - Ada compresses port values: 2 (0.3%)  
    - Ada converts and adjusts IP addresses: 16 (2.1%)  
- Different validation rules: 119 (15.5%)  
    - Port overflow: 6 (0.8%)  
    - Ada does not accept empty references: 5 (0.7%)  
    - Ada accepts URLs with no scheme and colons: 9 (1.2%)  
    - Ada accepts URLs with invalid octets: 14 (1.8%)  
    - URLs considered invalid by Ada: 15 (2.0%)  
    - Ada rejects empty hostname: 15 (2.0%)  
    - Ada fails on paths that resemble hosts: 2 (0.3%)  
    - Ada rejects URLs with invalid encoded unicode: 16 (2.1%)  
    - Ada rejects overflow IPv4 which are valid reg-names: 30 (3.9%)  
    - Ada accepts multiple @ in authority: 3 (0.4%)  
    - Ada rejects components that look like numbers: 4 (0.5%)  
- Special scheme-based treatment: 103 (13.4%)  
    - Ada has special logic for file paths: 23 (3.0%)  
    - Ada rejects file URLs with ports: 2 (0.3%)  
    - Invalid chars fixed in file: URLs: 10 (1.3%)  
    - Ada normalizes file URLs differently: 2 (0.3%)  
    - Ada rejects invalid IDNAs: 62 (8.1%)  
    - Ada rejects http URLs with no authority: 1 (0.1%)  
    - Special logic for unknown schemes: 3 (0.4%)  
- Ada accepts URLs with unicode: 47 (6.1%)  
    - Ada accepts URLs with unicode: 47 (6.1%)  
- Different modification rules: 46 (6.0%)  
    - Ada lets paths become hostnames: 17 (2.2%)  
    - Boost.URL normalizes octets when resolving URLs: 10 (1.3%)  
    - Ada removes dotdot segments above root (errata 4547): 16 (2.1%)  
    - Ada lets path become authority: 3 (0.4%)  
  
</details>  
  
When the expected results don't match, the fixture in this PR includes the reason. In all cases, Boost.URL is coherent with the intended behavior we have decided.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-03-16 16:54:47 UTC  
> Url: https://github.com/boostorg/url/pull/714#issuecomment-1472348811  

GCOVR code coverage report [https://714.url.prtest.cppalliance.org/gcovr/index.html](https://714.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://714.url.prtest.cppalliance.org/genhtml/index.html](https://714.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://714.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://714.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-03-16 17:21:46 UTC  
> Url: https://github.com/boostorg/url/pull/714#issuecomment-1472393488  

GCOVR code coverage report [https://714.url.prtest.cppalliance.org/gcovr/index.html](https://714.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://714.url.prtest.cppalliance.org/genhtml/index.html](https://714.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://714.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://714.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-03-16 17:59:06 UTC  
> Url: https://github.com/boostorg/url/pull/714#issuecomment-1472457036  

GCOVR code coverage report [https://714.url.prtest.cppalliance.org/gcovr/index.html](https://714.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://714.url.prtest.cppalliance.org/genhtml/index.html](https://714.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://714.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://714.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-03-16 18:34:28 UTC  
> Url: https://github.com/boostorg/url/pull/714#issuecomment-1472538793  

GCOVR code coverage report [https://714.url.prtest.cppalliance.org/gcovr/index.html](https://714.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://714.url.prtest.cppalliance.org/genhtml/index.html](https://714.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://714.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://714.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2023-03-16 18:37:40 UTC  
> Updated_at: 2023-03-16 18:40:21 UTC  
> Url: https://github.com/boostorg/url/pull/714#issuecomment-1472547497  

## [Codecov](https://codecov.io/gh/boostorg/url/pull/714?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#714](https://codecov.io/gh/boostorg/url/pull/714?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (026c79b) into [develop](https://codecov.io/gh/boostorg/url/commit/565de153d809e4d4d56771fb69c7bc25ecced1fc?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (565de15) will **increase** coverage by `0.10%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/714/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/714?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #714      +/-   ##  
===========================================  
+ Coverage    97.11%   97.21%   +0.10%       
===========================================  
  Files          155      155                
  Lines         8461     8486      +25       
===========================================  
+ Hits          8217     8250      +33       
+ Misses         244      236       -8       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/714?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/boostorg/url/pull/714?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `99.62% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/url/rfc/detail/impl/host\_rule.ipp](https://codecov.io/gh/boostorg/url/pull/714?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2RldGFpbC9pbXBsL2hvc3RfcnVsZS5pcHA=) | `100.00% <100.00%> (+2.43%)` | :arrow_up: |  
  
... and [5 files with indirect coverage changes](https://codecov.io/gh/boostorg/url/pull/714/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://codecov.io/gh/boostorg/url/pull/714?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/714?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [565de15...026c79b](https://codecov.io/gh/boostorg/url/pull/714?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
