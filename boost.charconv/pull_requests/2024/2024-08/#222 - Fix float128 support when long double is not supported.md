# #222 Fix float128 support when long double is not supported [Merged]

> Username: iv-m  
> Created at: 2024-08-27 08:38:10 UTC  
> Updated at: 2024-08-27 13:53:59 UTC  
> Merged at: 2024-08-27 13:52:54 UTC  
> Closed at: 2024-08-27 13:52:55 UTC  
> Url: https://github.com/boostorg/charconv/pull/222  

The idea of selecting type_layout here is:  
- find the matching layout if one exists;  
- otherwise, select the largest layout supported.  
  
Expressing this in a bit more clear way hides unsupported layouts from the compiler with preprocessor conditions, which fixes compilation when float128 is supported, but long doubles are not supported by Boost.Charconv on a given platform.  
  
Fixes #220.

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2024-08-27 12:33:43 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/charconv/pull/222#pullrequestreview-2263207177  

Looks good to me. It's more clear than: https://github.com/boostorg/charconv/pull/221 so I will close that in favor of this. Thank you!

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-08-27 12:38:34 UTC  
> Updated_at: 2024-08-27 13:53:59 UTC  
> Url: https://github.com/boostorg/charconv/pull/222#issuecomment-2312450499  

## [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/222?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.54%. Comparing base [(`0c10bd5`)](https://app.codecov.io/gh/boostorg/charconv/commit/0c10bd563dd33bb127ac61b276b0ed82fc95be27?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`77cf605`)](https://app.codecov.io/gh/boostorg/charconv/commit/77cf605d5e4e8bc98cfb27a15041505642fcb9d5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/charconv/pull/222/graphs/tree.svg?width=650&height=150&src=pr&token=jTnxtbJoTv&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/charconv/pull/222?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #222   +/-   ##  
========================================  
  Coverage    94.54%   94.54%             
========================================  
  Files           67       67             
  Lines         8490     8490             
========================================  
  Hits          8027     8027             
  Misses         463      463             
```  
  
| [Files](https://app.codecov.io/gh/boostorg/charconv/pull/222?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/to\_chars\_float\_impl.hpp](https://app.codecov.io/gh/boostorg/charconv/pull/222?src=pr&el=tree&filepath=src%2Fto_chars_float_impl.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3RvX2NoYXJzX2Zsb2F0X2ltcGwuaHBw) | `85.71% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/charconv/pull/222?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/charconv/pull/222?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0c10bd5...77cf605](https://app.codecov.io/gh/boostorg/charconv/pull/222?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
