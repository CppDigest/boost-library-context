# #115 Fix `from_chars` handling of non-finite 80 and 128-bit values [Merged]

> Username: mborland  
> Created at: 2024-01-08 07:13:48 UTC  
> Updated at: 2024-01-09 07:07:46 UTC  
> Merged at: 2024-01-09 07:07:43 UTC  
> Closed at: 2024-01-09 07:07:43 UTC  
> Url: https://github.com/boostorg/charconv/pull/115  

Closes: #114

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-01-08 08:32:02 UTC  
> Updated_at: 2024-01-09 06:26:43 UTC  
> Url: https://github.com/boostorg/charconv/pull/115#issuecomment-1880565701  

## [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/115?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`319b7c6`)](https://app.codecov.io/gh/cppalliance/charconv/commit/319b7c676493b9f71f78593cd5f4988b192ba4ac?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 86.22% compared to head [(`6eef3c2`)](https://app.codecov.io/gh/cppalliance/charconv/pull/115?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 86.60%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/charconv/pull/115/graphs/tree.svg?width=650&height=150&src=pr&token=7B68uj8tl4&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/charconv/pull/115?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #115      +/-   ##  
===========================================  
+ Coverage    86.22%   86.60%   +0.38%       
===========================================  
  Files           55       55                
  Lines         7891     7990      +99       
===========================================  
+ Hits          6804     6920     +116       
+ Misses        1087     1070      -17       
```  
  
  
| [Files](https://app.codecov.io/gh/cppalliance/charconv/pull/115?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [...de/boost/charconv/detail/from\_chars\_float\_impl.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/115?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZnJvbV9jaGFyc19mbG9hdF9pbXBsLmhwcA==) | `65.71% <100.00%> (+7.38%)` | :arrow_up: |  
| [include/boost/charconv/detail/parser.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/115?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvcGFyc2VyLmhwcA==) | `74.61% <100.00%> (+3.77%)` | :arrow_up: |  
| [src/from\_chars.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/115?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-c3JjL2Zyb21fY2hhcnMuY3Bw) | `97.77% <100.00%> (+0.63%)` | :arrow_up: |  
| [test/from\_chars\_float.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/115?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9mcm9tX2NoYXJzX2Zsb2F0LmNwcA==) | `98.58% <100.00%> (+0.07%)` | :arrow_up: |  
| [test/test\_float128.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/115?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC90ZXN0X2Zsb2F0MTI4LmNwcA==) | `100.00% <ø> (ø)` | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/cppalliance/charconv/pull/115/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/charconv/pull/115?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/115?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [319b7c6...6eef3c2](https://app.codecov.io/gh/cppalliance/charconv/pull/115?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
  
</details>

---
