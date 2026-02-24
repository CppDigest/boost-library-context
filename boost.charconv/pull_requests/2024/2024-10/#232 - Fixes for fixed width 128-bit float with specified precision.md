# #232 Fixes for fixed width 128-bit float with specified precision [Merged]

> Username: mborland  
> Created at: 2024-10-18 16:37:08 UTC  
> Updated at: 2024-10-28 17:19:41 UTC  
> Merged at: 2024-10-28 17:18:28 UTC  
> Closed at: 2024-10-28 17:18:28 UTC  
> Url: https://github.com/boostorg/charconv/pull/232  

Also updates the codecov runner to GCC-13 and includes GNU runs so that we can get coverage of the `<stdfloat>` paths.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-10-21 13:53:45 UTC  
> Updated_at: 2024-10-28 17:19:41 UTC  
> Url: https://github.com/boostorg/charconv/pull/232#issuecomment-2426754391  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/232?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `99.00000%` with `1 line` in your changes missing coverage. Please review.  
> Project coverage is 94.76%. Comparing base [(`e875722`)](https://app.codecov.io/gh/boostorg/charconv/commit/e875722bd6a2a02d6bcad9ae1c3a143ea754e3d9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`8cdcb5e`)](https://app.codecov.io/gh/boostorg/charconv/commit/8cdcb5e3f38bceef37a9fb0fdb8e2475d3455a6e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 29 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/charconv/pull/232?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [...lude/boost/charconv/detail/ryu/ryu\_generic\_128.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/232?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fryu%2Fryu_generic_128.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvcnl1L3J5dV9nZW5lcmljXzEyOC5ocHA=) | 97.14% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/charconv/pull/232?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/232/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/232?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #232      +/-   ##  
===========================================  
+ Coverage    94.63%   94.76%   +0.12%       
===========================================  
  Files           67       69       +2       
  Lines         8673     9031     +358       
===========================================  
+ Hits          8208     8558     +350       
- Misses         465      473       +8       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/charconv/pull/232?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/to\_chars.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/232?src=pr&el=tree&filepath=src%2Fto_chars.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3RvX2NoYXJzLmNwcA==) | `92.94% <ø> (-0.95%)` | :arrow_down: |  
| [test/github\_issue\_166\_float128.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/232?src=pr&el=tree&filepath=test%2Fgithub_issue_166_float128.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMTY2X2Zsb2F0MTI4LmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/to\_chars\_float\_STL\_comp.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/232?src=pr&el=tree&filepath=test%2Fto_chars_float_STL_comp.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90b19jaGFyc19mbG9hdF9TVExfY29tcC5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [...lude/boost/charconv/detail/ryu/ryu\_generic\_128.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/232?src=pr&el=tree&filepath=include%2Fboost%2Fcharconv%2Fdetail%2Fryu%2Fryu_generic_128.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9jaGFyY29udi9kZXRhaWwvcnl1L3J5dV9nZW5lcmljXzEyOC5ocHA=) | `92.72% <97.14%> (+4.86%)` | :arrow_up: |  
  
... and [13 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/charconv/pull/232/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/232?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/232?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e875722...8cdcb5e](https://app.codecov.io/gh/boostorg/charconv/pull/232?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
