# #117 Merge to Master [Merged]

> Username: mborland  
> Created at: 2024-01-11 08:58:19 UTC  
> Updated at: 2024-01-12 05:46:23 UTC  
> Merged at: 2024-01-12 05:46:23 UTC  
> Closed at: 2024-01-12 05:46:23 UTC  
> Url: https://github.com/boostorg/charconv/pull/117  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-01-11 09:33:15 UTC  
> Updated_at: 2024-01-11 09:48:07 UTC  
> Url: https://github.com/boostorg/charconv/pull/117#issuecomment-1886715433  

## [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/117?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`59b61b1`)](https://app.codecov.io/gh/cppalliance/charconv/commit/59b61b1b1a71b25d38c8815d26b39328090365f3?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 86.22% compared to head [(`612bdf9`)](https://app.codecov.io/gh/cppalliance/charconv/pull/117?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.02%.  
> Report is 5 commits behind head on master.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/charconv/pull/117/graphs/tree.svg?width=650&height=150&src=pr&token=7B68uj8tl4&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/charconv/pull/117?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@            Coverage Diff             @@  
##           master     #117      +/-   ##  
==========================================  
+ Coverage   86.22%   88.02%   +1.80%       
==========================================  
  Files          55       55                
  Lines        7891     7861      -30       
==========================================  
+ Hits         6804     6920     +116       
+ Misses       1087      941     -146       
```  
  
  
| [Files](https://app.codecov.io/gh/cppalliance/charconv/pull/117?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [...de/boost/charconv/detail/from\_chars\_float\_impl.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/117?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZnJvbV9jaGFyc19mbG9hdF9pbXBsLmhwcA==) | `65.71% <100.00%> (+7.38%)` | :arrow_up: |  
| [include/boost/charconv/detail/parser.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/117?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvcGFyc2VyLmhwcA==) | `74.61% <100.00%> (+3.77%)` | :arrow_up: |  
| [src/from\_chars.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/117?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-c3JjL2Zyb21fY2hhcnMuY3Bw) | `97.77% <100.00%> (+0.63%)` | :arrow_up: |  
| [test/from\_chars\_float.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/117?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9mcm9tX2NoYXJzX2Zsb2F0LmNwcA==) | `98.58% <100.00%> (+0.07%)` | :arrow_up: |  
| [test/from\_chars\_float2.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/117?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9mcm9tX2NoYXJzX2Zsb2F0Mi5jcHA=) | `100.00% <ø> (+20.00%)` | :arrow_up: |  
| [test/limits.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/117?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9saW1pdHMuY3Bw) | `100.00% <ø> (+31.31%)` | :arrow_up: |  
| [test/roundtrip.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/117?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9yb3VuZHRyaXAuY3Bw) | `98.42% <ø> (+10.57%)` | :arrow_up: |  
| [test/test\_128bit\_emulation.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/117?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC90ZXN0XzEyOGJpdF9lbXVsYXRpb24uY3Bw) | `100.00% <ø> (+19.83%)` | :arrow_up: |  
| [test/test\_boost\_json\_values.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/117?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC90ZXN0X2Jvb3N0X2pzb25fdmFsdWVzLmNwcA==) | `99.59% <ø> (+2.39%)` | :arrow_up: |  
| [test/test\_float128.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/117?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC90ZXN0X2Zsb2F0MTI4LmNwcA==) | `100.00% <ø> (ø)` | |  
| ... and [3 more](https://app.codecov.io/gh/cppalliance/charconv/pull/117?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | |  
  
... and [2 files with indirect coverage changes](https://app.codecov.io/gh/cppalliance/charconv/pull/117/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/charconv/pull/117?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/117?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [59b61b1...612bdf9](https://app.codecov.io/gh/cppalliance/charconv/pull/117?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
  
</details>

---
