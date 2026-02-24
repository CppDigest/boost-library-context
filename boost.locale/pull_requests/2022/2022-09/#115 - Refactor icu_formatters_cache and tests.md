# #115 Refactor icu_formatters_cache and tests [Merged]

> Username: Flamefire  
> Created at: 2022-09-26 13:33:24 UTC  
> Updated at: 2022-09-27 19:58:47 UTC  
> Merged at: 2022-09-27 19:58:45 UTC  
> Closed at: 2022-09-27 19:58:45 UTC  
> Url: https://github.com/boostorg/locale/pull/115  

- Refactor test macros to make them a bit more efficient and easier to step through  
- Introduce enum `format_len` to be used as indices for the ICU cache replacing the magic constants.  
- Rename `test_config` to `show_config` (more appropriate name) and always show its output on CI (workaround until B2 natively supports that, CC @grafikrobot in case you want to see a use case)

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-09-27 19:57:55 UTC  
> Url: https://github.com/boostorg/locale/pull/115#issuecomment-1259983084  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/115?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#115](https://codecov.io/gh/boostorg/locale/pull/115?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (04cd74d) into [develop](https://codecov.io/gh/boostorg/locale/commit/e7a54647dcc31ccea177e230fa7d65af1afdc0f7?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e7a5464) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/115/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/115?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #115   +/-   ##  
========================================  
  Coverage    84.20%   84.20%             
========================================  
  Files           73       73             
  Lines         5794     5794             
========================================  
  Hits          4879     4879             
  Misses         915      915             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/115?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/show\_config.cpp](https://codecov.io/gh/boostorg/locale/pull/115/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9zaG93X2NvbmZpZy5jcHA=) | `96.22% <ø> (ø)` | |  
| [test/test\_formatting.cpp](https://codecov.io/gh/boostorg/locale/pull/115/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Zvcm1hdHRpbmcuY3Bw) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/115?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/115?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e7a5464...04cd74d](https://codecov.io/gh/boostorg/locale/pull/115?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
