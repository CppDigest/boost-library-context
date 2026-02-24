# #172 Merge to Master for 1.85 [Merged]

> Username: mborland  
> Created at: 2024-02-27 13:30:17 UTC  
> Updated at: 2024-02-27 14:21:14 UTC  
> Merged at: 2024-02-27 14:21:14 UTC  
> Closed at: 2024-02-27 14:21:14 UTC  
> Url: https://github.com/boostorg/charconv/pull/172  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-27 14:08:21 UTC  
> Updated_at: 2024-02-27 14:19:22 UTC  
> Url: https://github.com/boostorg/charconv/pull/172#issuecomment-1966633503  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/172?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `91.88970%` with `100 lines` in your changes are missing coverage. Please review.  
> :exclamation: No coverage uploaded for pull request base (`master@b404767`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#section-missing-base-commit).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/172/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/172?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##             master     #172   +/-   ##  
=========================================  
  Coverage          ?   93.29%             
=========================================  
  Files             ?       65             
  Lines             ?     8571             
  Branches          ?        0             
=========================================  
  Hits              ?     7996             
  Misses            ?      575             
  Partials          ?        0             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/charconv/pull/172?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/charconv/detail/buffer\_sizing.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/172?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvYnVmZmVyX3NpemluZy5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/charconv/detail/compute\_float32.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/172?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvY29tcHV0ZV9mbG9hdDMyLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/charconv/detail/compute\_float80.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/172?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvY29tcHV0ZV9mbG9hdDgwLmhwcA==) | `91.30% <100.00%> (ø)` | |  
| [.../boost/charconv/detail/fast\_float/float\_common.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/172?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZmFzdF9mbG9hdC9mbG9hdF9jb21tb24uaHBw) | `88.79% <100.00%> (ø)` | |  
| [.../boost/charconv/detail/from\_chars\_integer\_impl.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/172?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvZnJvbV9jaGFyc19pbnRlZ2VyX2ltcGwuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/charconv/detail/ryu/generic\_128.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/172?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvcnl1L2dlbmVyaWNfMTI4LmhwcA==) | `79.04% <100.00%> (ø)` | |  
| [include/boost/charconv/from\_chars.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/172?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9mcm9tX2NoYXJzLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/charconv/to\_chars.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/172?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi90b19jaGFycy5ocHA=) | `100.00% <ø> (ø)` | |  
| [src/from\_chars\_float\_impl.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/172?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Zyb21fY2hhcnNfZmxvYXRfaW1wbC5ocHA=) | `82.97% <ø> (ø)` | |  
| [test/from\_chars.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/172?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9mcm9tX2NoYXJzLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| ... and [28 more](https://app.codecov.io/gh/boostorg/charconv/pull/172?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/172?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/172?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b404767...d1236ab](https://app.codecov.io/gh/boostorg/charconv/pull/172?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
