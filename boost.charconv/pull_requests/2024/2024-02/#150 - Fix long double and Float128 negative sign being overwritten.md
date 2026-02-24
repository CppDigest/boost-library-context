# #150 Fix long double and Float128 negative sign being overwritten [Merged]

> Username: mborland  
> Created at: 2024-02-02 08:07:52 UTC  
> Updated at: 2024-02-02 08:44:04 UTC  
> Merged at: 2024-02-02 08:44:02 UTC  
> Closed at: 2024-02-02 08:44:02 UTC  
> Url: https://github.com/boostorg/charconv/pull/150  

Closes: #148

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-02 08:43:21 UTC  
> Url: https://github.com/boostorg/charconv/pull/150#issuecomment-1923338210  

## [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/150?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`48b7098`)](https://app.codecov.io/gh/cppalliance/charconv/commit/48b7098c570f75d9a1ce220df289bd900eec841f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.41% compared to head [(`825a24b`)](https://app.codecov.io/gh/cppalliance/charconv/pull/150?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.53%.  
> Report is 16 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/charconv/pull/150/graphs/tree.svg?width=650&height=150&src=pr&token=7B68uj8tl4&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/charconv/pull/150?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #150      +/-   ##  
===========================================  
+ Coverage    88.41%   88.53%   +0.11%       
===========================================  
  Files           58       59       +1       
  Lines         7936     8033      +97       
===========================================  
+ Hits          7017     7112      +95       
- Misses         919      921       +2       
```  
  
  
| [Files](https://app.codecov.io/gh/cppalliance/charconv/pull/150?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [...lude/boost/charconv/detail/ryu/ryu\_generic\_128.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/150?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvcnl1L3J5dV9nZW5lcmljXzEyOC5ocHA=) | `82.40% <100.00%> (+0.37%)` | :arrow_up: |  
| [test/roundtrip.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/150?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9yb3VuZHRyaXAuY3Bw) | `98.44% <100.00%> (+0.01%)` | :arrow_up: |  
| [test/test\_float128.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/150?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC90ZXN0X2Zsb2F0MTI4LmNwcA==) | `100.00% <ø> (ø)` | |  
  
... and [5 files with indirect coverage changes](https://app.codecov.io/gh/cppalliance/charconv/pull/150/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/charconv/pull/150?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/150?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [48b7098...825a24b](https://app.codecov.io/gh/cppalliance/charconv/pull/150?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
  
</details>

---
