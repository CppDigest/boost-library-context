# #159 Fix 80-bit long double hex formatting [Merged]

> Username: mborland  
> Created at: 2024-02-08 09:44:04 UTC  
> Updated at: 2024-02-09 10:08:16 UTC  
> Merged at: 2024-02-09 10:08:14 UTC  
> Closed at: 2024-02-09 10:08:14 UTC  
> Url: https://github.com/boostorg/charconv/pull/159  

Closes: #154

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-02-09 10:05:19 UTC  
> Url: https://github.com/boostorg/charconv/pull/159#issuecomment-1935643635  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/159?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: `1 lines` in your changes are missing coverage. Please review.  
> Comparison is base [(`51fc3f4`)](https://app.codecov.io/gh/boostorg/charconv/commit/51fc3f40ba0bb46e9bed5d772a2a242adb297836?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 88.62% compared to head [(`547b0ad`)](https://app.codecov.io/gh/boostorg/charconv/pull/159?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 88.64%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/159/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/159?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #159      +/-   ##  
===========================================  
+ Coverage    88.62%   88.64%   +0.01%       
===========================================  
  Files           63       64       +1       
  Lines         8141     8162      +21       
===========================================  
+ Hits          7215     7235      +20       
- Misses         926      927       +1       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/charconv/pull/159?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/github\_issue\_154.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/159?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9naXRodWJfaXNzdWVfMTU0LmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [test/to\_chars\_float\_STL\_comp.cpp](https://app.codecov.io/gh/boostorg/charconv/pull/159?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90b19jaGFyc19mbG9hdF9TVExfY29tcC5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [src/to\_chars\_float\_impl.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/159?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3RvX2NoYXJzX2Zsb2F0X2ltcGwuaHBw) | `94.82% <94.11%> (-0.30%)` | :arrow_down: |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/charconv/pull/159/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/159?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/159?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [51fc3f4...547b0ad](https://app.codecov.io/gh/boostorg/charconv/pull/159?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
