# #146 Post-Review Refactoring [Merged]

> Username: mborland  
> Created at: 2024-01-30 09:54:27 UTC  
> Updated at: 2024-01-31 07:16:39 UTC  
> Merged at: 2024-01-31 07:16:35 UTC  
> Closed at: 2024-01-31 07:16:36 UTC  
> Url: https://github.com/boostorg/charconv/pull/146  

Closes: #126   
  
- Moves implementation files into the src directory  
- Moves giant lookup tables into structures  
- Fixes some more instances of old casts and sign conversion  
- Fixes return type of clinger's fast path for `__float128`

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-01-30 13:30:24 UTC  
> Url: https://github.com/boostorg/charconv/pull/146#issuecomment-1916853113  

## [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/146?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
Attention: `28 lines` in your changes are missing coverage. Please review.  
> Comparison is base [(`e6a91fb`)](https://app.codecov.io/gh/cppalliance/charconv/commit/e6a91fb9b81e5a5ccfb73d6cd072e2b4afd77dc2?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.41% compared to head [(`6c30488`)](https://app.codecov.io/gh/cppalliance/charconv/pull/146?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) 88.41%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/charconv/pull/146/graphs/tree.svg?width=650&height=150&src=pr&token=7B68uj8tl4&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/charconv/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #146   +/-   ##  
========================================  
  Coverage    88.41%   88.41%             
========================================  
  Files           56       58    +2       
  Lines         7935     7936    +1       
========================================  
+ Hits          7016     7017    +1       
  Misses         919      919             
```  
  
  
| [Files](https://app.codecov.io/gh/cppalliance/charconv/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/charconv/detail/compute\_float80.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvY29tcHV0ZV9mbG9hdDgwLmhwcA==) | `91.30% <100.00%> (ø)` | |  
| [.../boost/charconv/detail/fast\_float/float\_common.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZmFzdF9mbG9hdC9mbG9hdF9jb21tb24uaHBw) | `88.79% <100.00%> (ø)` | |  
| [include/boost/charconv/detail/ryu/generic\_128.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvcnl1L2dlbmVyaWNfMTI4LmhwcA==) | `79.04% <100.00%> (ø)` | |  
| [...de/boost/charconv/detail/to\_chars\_integer\_impl.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvdG9fY2hhcnNfaW50ZWdlcl9pbXBsLmhwcA==) | `96.89% <ø> (ø)` | |  
| [include/boost/charconv/to\_chars.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi90b19jaGFycy5ocHA=) | `100.00% <ø> (+9.69%)` | :arrow_up: |  
| [src/from\_chars.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-c3JjL2Zyb21fY2hhcnMuY3Bw) | `100.00% <ø> (ø)` | |  
| [src/from\_chars\_float\_impl.hpp](https://app.codecov.io/gh/cppalliance/charconv/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-c3JjL2Zyb21fY2hhcnNfZmxvYXRfaW1wbC5ocHA=) | `82.97% <ø> (ø)` | |  
| [src/to\_chars.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-c3JjL3RvX2NoYXJzLmNwcA==) | `92.24% <ø> (ø)` | |  
| [test/from\_chars\_float.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9mcm9tX2NoYXJzX2Zsb2F0LmNwcA==) | `98.59% <ø> (ø)` | |  
| [test/github\_issue\_122.cpp](https://app.codecov.io/gh/cppalliance/charconv/pull/146?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC9naXRodWJfaXNzdWVfMTIyLmNwcA==) | `100.00% <ø> (ø)` | |  
| ... and [6 more](https://app.codecov.io/gh/cppalliance/charconv/pull/146?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/charconv/pull/146?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/charconv/pull/146?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [e6a91fb...6c30488](https://app.codecov.io/gh/cppalliance/charconv/pull/146?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
  
</details>

---
