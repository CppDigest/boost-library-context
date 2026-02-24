# #141 Reverse behavior of from_chars and from_chars_strict [Merged]

> Username: mborland  
> Created at: 2024-01-26 11:03:41 UTC  
> Updated at: 2024-01-29 10:49:04 UTC  
> Merged at: 2024-01-29 10:49:02 UTC  
> Closed at: 2024-01-29 10:49:02 UTC  
> Url: https://github.com/boostorg/charconv/pull/141  

Reverse the behavior from https://github.com/cppalliance/charconv/issues/110. Discussion during review and on slack has people more concerned with boost components of same name matching the behavior of the standard library exactly. Add from_chars_erange for the change handling of `ERANGE`

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-01-26 13:28:15 UTC  
> Updated_at: 2024-01-26 19:17:31 UTC  
> Url: https://github.com/boostorg/charconv/pull/141#issuecomment-1912068297  

## [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/141?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`850a1e2`)](https://app.codecov.io/gh/cppalliance/charconv/commit/850a1e21b58b2d3859535bf8ed8842f0ab41cf88?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.29% compared to head [(`39ca633`)](https://app.codecov.io/gh/cppalliance/charconv/pull/141?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.30%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/charconv/pull/141/graphs/tree.svg?width=650&height=150&src=pr&token=7B68uj8tl4&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/charconv/pull/141?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #141      +/-   ##  
===========================================  
+ Coverage    88.29%   88.30%   +0.01%       
===========================================  
  Files           56       56                
  Lines         7919     7919                
===========================================  
+ Hits          6992     6993       +1       
+ Misses         927      926       -1       
```  
  
  
| [Files](https://app.codecov.io/gh/cppalliance/charconv/pull/141?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/charconv/from\_chars.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/141?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9mcm9tX2NoYXJzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [src/from\_chars.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/141?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-c3JjL2Zyb21fY2hhcnMuY3Bw) | `100.00% <100.00%> (+2.08%)` | :arrow_up: |  
| [test/from\_chars\_float.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/141?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9mcm9tX2NoYXJzX2Zsb2F0LmNwcA==) | `98.58% <100.00%> (ø)` | |  
| [test/github\_issue\_110.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/141?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9naXRodWJfaXNzdWVfMTEwLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/test\_boost\_json\_values.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/141?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC90ZXN0X2Jvb3N0X2pzb25fdmFsdWVzLmNwcA==) | `99.59% <100.00%> (ø)` | |  
| [test/test\_float128.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/141?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC90ZXN0X2Zsb2F0MTI4LmNwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/charconv/pull/141?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/141?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [850a1e2...39ca633](https://app.codecov.io/gh/cppalliance/charconv/pull/141?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
  
</details>

---
