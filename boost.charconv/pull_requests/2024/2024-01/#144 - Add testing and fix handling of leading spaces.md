# #144 Add testing and fix handling of leading spaces [Merged]

> Username: mborland  
> Created at: 2024-01-29 08:06:51 UTC  
> Updated at: 2024-01-29 08:49:50 UTC  
> Merged at: 2024-01-29 08:49:47 UTC  
> Closed at: 2024-01-29 08:49:47 UTC  
> Url: https://github.com/boostorg/charconv/pull/144  

Closes: #142

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-01-29 08:31:16 UTC  
> Updated_at: 2024-01-29 08:32:24 UTC  
> Url: https://github.com/boostorg/charconv/pull/144#issuecomment-1914198485  

## [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/144?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`850a1e2`)](https://app.codecov.io/gh/cppalliance/charconv/commit/850a1e21b58b2d3859535bf8ed8842f0ab41cf88?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.29% compared to head [(`395c4d3`)](https://app.codecov.io/gh/cppalliance/charconv/pull/144?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.40%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/charconv/pull/144/graphs/tree.svg?width=650&height=150&src=pr&token=7B68uj8tl4&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/charconv/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #144      +/-   ##  
===========================================  
+ Coverage    88.29%   88.40%   +0.11%       
===========================================  
  Files           56       56                
  Lines         7919     7935      +16       
===========================================  
+ Hits          6992     7015      +23       
+ Misses         927      920       -7       
```  
  
  
| [Files](https://app.codecov.io/gh/cppalliance/charconv/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [.../boost/charconv/detail/from\_chars\_integer\_impl.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZnJvbV9jaGFyc19pbnRlZ2VyX2ltcGwuaHBw) | `100.00% <100.00%> (ø)` | |  
| [test/from\_chars.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9mcm9tX2NoYXJzLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/from\_chars\_float.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/144?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9mcm9tX2NoYXJzX2Zsb2F0LmNwcA==) | `98.59% <100.00%> (+<0.01%)` | :arrow_up: |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/cppalliance/charconv/pull/144/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/charconv/pull/144?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/144?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [850a1e2...395c4d3](https://app.codecov.io/gh/cppalliance/charconv/pull/144?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
  
</details>

---
