# #198 Fix issue with negative times in `date_time` and add tests for full coverage [Merged]

> Username: Flamefire  
> Created at: 2023-10-11 11:20:44 UTC  
> Updated at: 2023-10-12 07:08:42 UTC  
> Merged at: 2023-10-12 07:08:40 UTC  
> Closed at: 2023-10-12 07:08:40 UTC  
> Url: https://github.com/boostorg/locale/pull/198  

When handling negative times (`double` value) the fractional part (i.e. nanoseconds) is subtracted from the seconds to make the nanoseconds positive. However the subtraction happened on a temporary value, not the result struct leading to off-by-one errors  
  
Also add some more tests to fully cover `date_time.cpp` (which led to the discovery of the above bug),  
especially the `date_time` constructors taking a `calendar` and the handling of the "active" time-zone were not sufficiently tested

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-10-11 22:06:56 UTC  
> Url: https://github.com/boostorg/locale/pull/198#issuecomment-1758614605  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/198?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#198](https://app.codecov.io/gh/boostorg/locale/pull/198?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (303e141) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/2cdef1a6f97488e7827697a6201da5239051c9bf?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2cdef1a) will **increase** coverage by `0.32%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/198/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/198?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #198      +/-   ##  
===========================================  
+ Coverage    94.93%   95.26%   +0.32%       
===========================================  
  Files          114      114                
  Lines         9917    10001      +84       
===========================================  
+ Hits          9415     9527     +112       
+ Misses         502      474      -28       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/locale/pull/198?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/date\_time.hpp](https://app.codecov.io/gh/boostorg/locale/pull/198?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZGF0ZV90aW1lLmhwcA==) | `95.03% <100.00%> (+0.10%)` | :arrow_up: |  
| [include/boost/locale/hold\_ptr.hpp](https://app.codecov.io/gh/boostorg/locale/pull/198?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvaG9sZF9wdHIuaHBw) | `94.44% <100.00%> (-1.02%)` | :arrow_down: |  
| [src/boost/locale/shared/date\_time.cpp](https://app.codecov.io/gh/boostorg/locale/pull/198?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9zaGFyZWQvZGF0ZV90aW1lLmNwcA==) | `100.00% <100.00%> (+12.90%)` | :arrow_up: |  
| [test/test\_date\_time.cpp](https://app.codecov.io/gh/boostorg/locale/pull/198?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2RhdGVfdGltZS5jcHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/198?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/198?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2cdef1a...303e141](https://app.codecov.io/gh/boostorg/locale/pull/198?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
