# #708 path normalization updates parameters [Merged]

> Username: alandefreitas  
> Created at: 2023-03-08 00:00:26 UTC  
> Updated at: 2024-02-21 00:55:20 UTC  
> Merged at: 2023-03-08 04:12:08 UTC  
> Closed at: 2023-03-08 04:12:08 UTC  
> Url: https://github.com/boostorg/url/pull/708  

fix #674  
  
This PR fixes the second issue in https://github.com/boostorg/url/issues/674  
  
It was also a failure to handle an exception from the general case related to our convention that `"/"` -> `{}` and `"/./"` -> `{""}`. This caused unexpected `set_path` to have unexpected results.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-03-08 00:15:29 UTC  
> Url: https://github.com/boostorg/url/pull/708#issuecomment-1459070366  

GCOVR code coverage report [https://708.url.prtest.cppalliance.org/gcovr/index.html](https://708.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://708.url.prtest.cppalliance.org/genhtml/index.html](https://708.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://708.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://708.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-03-08 00:55:16 UTC  
> Updated_at: 2023-03-08 00:55:18 UTC  
> Url: https://github.com/boostorg/url/pull/708#issuecomment-1459099437  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/708?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#708](https://codecov.io/gh/boostorg/url/pull/708?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9ab5d3a) into [develop](https://codecov.io/gh/boostorg/url/commit/23e010933bc6c63dd2dc83bb09b602a7fc3c0d0f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (23e0109) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/708/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/708?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #708   +/-   ##  
========================================  
  Coverage    97.13%   97.13%             
========================================  
  Files          155      155             
  Lines         8449     8457    +8       
========================================  
+ Hits          8207     8215    +8       
  Misses         242      242             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/708?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/boostorg/url/pull/708?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `99.75% <100.00%> (+<0.01%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/708?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/708?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [23e0109...9ab5d3a](https://codecov.io/gh/boostorg/url/pull/708?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-03-08 00:55:40 UTC  
> Url: https://github.com/boostorg/url/pull/708#issuecomment-1459099704  

GCOVR code coverage report [https://708.url.prtest.cppalliance.org/gcovr/index.html](https://708.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://708.url.prtest.cppalliance.org/genhtml/index.html](https://708.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://708.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://708.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
