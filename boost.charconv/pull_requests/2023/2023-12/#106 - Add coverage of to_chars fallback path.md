# #106 Add coverage of to_chars fallback path [Merged]

> Username: mborland  
> Created at: 2023-12-08 08:19:17 UTC  
> Updated at: 2023-12-08 09:41:21 UTC  
> Merged at: 2023-12-08 09:41:13 UTC  
> Closed at: 2023-12-08 09:41:13 UTC  
> Url: https://github.com/boostorg/charconv/pull/106  

This path is only taken by extreme values on platforms with 128-bit long doubles so force direct testing.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-12-08 09:40:45 UTC  
> Url: https://github.com/boostorg/charconv/pull/106#issuecomment-1846864345  

## [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/106?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
> Merging [#106](https://app.codecov.io/gh/cppalliance/charconv/pull/106?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) (516601b) into [develop](https://app.codecov.io/gh/cppalliance/charconv/commit/f55167d66a77c6ac9eebe903a291cdfe330d1f4c?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) (f55167d) will **increase** coverage by `0.67%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/charconv/pull/106/graphs/tree.svg?width=650&height=150&src=pr&token=7B68uj8tl4&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/charconv/pull/106?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #106      +/-   ##  
===========================================  
+ Coverage    75.89%   76.56%   +0.67%       
===========================================  
  Files           28       28                
  Lines         3957     3955       -2       
===========================================  
+ Hits          3003     3028      +25       
+ Misses         954      927      -27       
```  
  
  
| [Files](https://app.codecov.io/gh/cppalliance/charconv/pull/106?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/charconv/to\_chars.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/106?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi90b19jaGFycy5ocHA=) | `88.42% <100.00%> (+13.94%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/charconv/pull/106?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/106?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [f55167d...516601b](https://app.codecov.io/gh/cppalliance/charconv/pull/106?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
  
</details>

---
