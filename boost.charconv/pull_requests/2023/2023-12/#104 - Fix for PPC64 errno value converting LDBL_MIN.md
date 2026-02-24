# #104 Fix for PPC64 errno value converting LDBL_MIN [Merged]

> Username: mborland  
> Created at: 2023-12-07 10:46:07 UTC  
> Updated at: 2023-12-07 12:40:30 UTC  
> Merged at: 2023-12-07 12:40:27 UTC  
> Closed at: 2023-12-07 12:40:27 UTC  
> Url: https://github.com/boostorg/charconv/pull/104  

Closes: #103

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-12-07 12:20:41 UTC  
> Updated_at: 2023-12-07 12:37:10 UTC  
> Url: https://github.com/boostorg/charconv/pull/104#issuecomment-1845246271  

## [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/104?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
> Merging [#104](https://app.codecov.io/gh/cppalliance/charconv/pull/104?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) (5b5850f) into [develop](https://app.codecov.io/gh/cppalliance/charconv/commit/ff8712ee5e12b81d5555fa2636f47e6cbb8ba453?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) (ff8712e) will **decrease** coverage by `0.02%`.  
> The diff coverage is `40.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/charconv/pull/104/graphs/tree.svg?width=650&height=150&src=pr&token=7B68uj8tl4&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/charconv/pull/104?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #104      +/-   ##  
===========================================  
- Coverage    75.90%   75.89%   -0.02%       
===========================================  
  Files           28       28                
  Lines         3955     3957       +2       
===========================================  
+ Hits          3002     3003       +1       
- Misses         953      954       +1       
```  
  
  
| [Files](https://app.codecov.io/gh/cppalliance/charconv/pull/104?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [src/from\_chars.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/104?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-c3JjL2Zyb21fY2hhcnMuY3Bw) | `97.14% <66.66%> (-2.86%)` | :arrow_down: |  
| [include/boost/charconv/to\_chars.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/104?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi90b19jaGFycy5ocHA=) | `74.47% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/charconv/pull/104?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/104?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [ff8712e...5b5850f](https://app.codecov.io/gh/cppalliance/charconv/pull/104?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
  
</details>

---
