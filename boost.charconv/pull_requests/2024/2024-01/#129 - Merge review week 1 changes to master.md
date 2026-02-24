# #129 Merge review week 1 changes to master [Merged]

> Username: mborland  
> Created at: 2024-01-22 09:39:15 UTC  
> Updated at: 2024-01-22 10:38:26 UTC  
> Merged at: 2024-01-22 10:38:26 UTC  
> Closed at: 2024-01-22 10:38:26 UTC  
> Url: https://github.com/boostorg/charconv/pull/129  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-01-22 10:11:57 UTC  
> Updated_at: 2024-01-22 10:28:35 UTC  
> Url: https://github.com/boostorg/charconv/pull/129#issuecomment-1903661222  

## [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/129?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
Attention: `4 lines` in your changes are missing coverage. Please review.  
> Comparison is base [(`06d8347`)](https://app.codecov.io/gh/cppalliance/charconv/commit/06d834728ae8970473f6ef18c079f0e7fae7b071?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.02% compared to head [(`b404767`)](https://app.codecov.io/gh/cppalliance/charconv/pull/129?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.29%.  
> Report is 7 commits behind head on master.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/charconv/pull/129/graphs/tree.svg?width=650&height=150&src=pr&token=7B68uj8tl4&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/charconv/pull/129?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #129      +/-   ##  
==========================================  
+ Coverage   88.02%   88.29%   +0.26%       
==========================================  
  Files          55       56       +1       
  Lines        7861     7919      +58       
==========================================  
+ Hits         6920     6992      +72       
+ Misses        941      927      -14       
```  
  
  
| [Files](https://app.codecov.io/gh/cppalliance/charconv/pull/129?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [...lude/boost/charconv/detail/dragonbox/dragonbox.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/129?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZHJhZ29uYm94L2RyYWdvbmJveC5ocHA=) | `97.28% <100.00%> (ø)` | |  
| [include/boost/charconv/from\_chars.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/129?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9mcm9tX2NoYXJzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/charconv/to\_chars.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/129?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi90b19jaGFycy5ocHA=) | `90.30% <100.00%> (ø)` | |  
| [test/from\_chars\_float.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/129?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9mcm9tX2NoYXJzX2Zsb2F0LmNwcA==) | `98.58% <ø> (ø)` | |  
| [test/github\_issue\_110.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/129?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9naXRodWJfaXNzdWVfMTEwLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/github\_issue\_122.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/129?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9naXRodWJfaXNzdWVfMTIyLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/roundtrip.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/129?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9yb3VuZHRyaXAuY3Bw) | `98.42% <100.00%> (ø)` | |  
| [test/test\_boost\_json\_values.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/129?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC90ZXN0X2Jvb3N0X2pzb25fdmFsdWVzLmNwcA==) | `99.59% <ø> (ø)` | |  
| [test/test\_float128.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/129?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC90ZXN0X2Zsb2F0MTI4LmNwcA==) | `100.00% <ø> (ø)` | |  
| [src/from\_chars.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/129?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-c3JjL2Zyb21fY2hhcnMuY3Bw) | `97.91% <91.66%> (+0.13%)` | :arrow_up: |  
| ... and [1 more](https://app.codecov.io/gh/cppalliance/charconv/pull/129?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/cppalliance/charconv/pull/129/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/charconv/pull/129?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/129?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [06d8347...b404767](https://app.codecov.io/gh/cppalliance/charconv/pull/129?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
  
</details>

---
