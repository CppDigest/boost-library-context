# #585 Reduce truncation in dec_float multiplication [Merged]

> Username: ckormanyos  
> Created at: 2024-01-19 05:42:05 UTC  
> Updated at: 2024-01-19 10:51:29 UTC  
> Merged at: 2024-01-19 10:51:15 UTC  
> Closed at: 2024-01-19 10:51:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/585  

The purpose of this PR is to reduce truncation in `cpp_dec_float`'s multiplication. It is based on some discussion in parts of #368.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-01-19 07:04:51 UTC  
> Updated_at: 2024-01-19 08:51:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/585#issuecomment-1899876187  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/585?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`e584f4f`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/e584f4f35dfa6eafe9720a78d678ac2663a835b0?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 94.1% compared to head [(`73fb4d3`)](https://app.codecov.io/gh/boostorg/multiprecision/pull/585?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 94.1%.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/585/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/585?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff           @@  
##           develop    #585   +/-   ##  
=======================================  
  Coverage     94.1%   94.1%             
=======================================  
  Files          273     273             
  Lines        28523   28523             
=======================================  
  Hits         26835   26835             
  Misses        1688    1688             
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/multiprecision/pull/585?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/multiprecision/cpp\_dec\_float.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/585?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jcHBfZGVjX2Zsb2F0LmhwcA==) | `94.0% <100.0%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/585?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/585?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e584f4f...73fb4d3](https://app.codecov.io/gh/boostorg/multiprecision/pull/585?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2024-01-19 07:12:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/585#issuecomment-1899884098  

Hi Matt (@mborland) I can configure the CodeCov Deltas. I'm a bit surprised that my trivial change led to such a decrease. I'll look into this.  
  
Anyway, we dont want fails due to a small one, two percent delta. I can handle this on the weekend in the CodeCov Properties for this workspace.

---
