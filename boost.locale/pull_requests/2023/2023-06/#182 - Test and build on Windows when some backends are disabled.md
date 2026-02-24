# #182 Test and build on Windows when some backends are disabled [Merged]

> Username: Flamefire  
> Created at: 2023-06-18 07:18:05 UTC  
> Updated at: 2023-06-22 17:02:29 UTC  
> Merged at: 2023-06-19 19:19:56 UTC  
> Closed at: 2023-06-19 19:19:57 UTC  
> Url: https://github.com/boostorg/locale/pull/182  

Add tests with disabled WinAPI and/or std backend and fix the detected issues.  
  
Fixes #180  
Fixes #181

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-06-18 12:33:07 UTC  
> Updated_at: 2023-06-18 21:54:06 UTC  
> Url: https://github.com/boostorg/locale/pull/182#issuecomment-1596129164  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/182?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#182](https://app.codecov.io/gh/boostorg/locale/pull/182?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2fe2030) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/e910a195e3fc98e2241ee9a2a4fac88c0f244a33?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e910a19) will **decrease** coverage by `0.01%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/182/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/182?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #182      +/-   ##  
===========================================  
- Coverage    92.69%   92.69%   -0.01%       
===========================================  
  Files          112      112                
  Lines         9820     9817       -3       
===========================================  
- Hits          9103     9100       -3       
  Misses         717      717                
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/locale/pull/182?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/show\_config.cpp](https://app.codecov.io/gh/boostorg/locale/pull/182?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC9zaG93X2NvbmZpZy5jcHA=) | `100.00% <ø> (ø)` | |  
| [test/test\_std\_collate.cpp](https://app.codecov.io/gh/boostorg/locale/pull/182?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N0ZF9jb2xsYXRlLmNwcA==) | `98.00% <ø> (ø)` | |  
| [test/test\_std\_convert.cpp](https://app.codecov.io/gh/boostorg/locale/pull/182?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N0ZF9jb252ZXJ0LmNwcA==) | `92.10% <ø> (ø)` | |  
| [test/test\_std\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/182?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3N0ZF9mb3JtYXR0aW5nLmNwcA==) | `99.20% <ø> (ø)` | |  
| [test/test\_winapi\_collate.cpp](https://app.codecov.io/gh/boostorg/locale/pull/182?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3dpbmFwaV9jb2xsYXRlLmNwcA==) | `88.40% <ø> (ø)` | |  
| [test/test\_winapi\_convert.cpp](https://app.codecov.io/gh/boostorg/locale/pull/182?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3dpbmFwaV9jb252ZXJ0LmNwcA==) | `89.47% <ø> (ø)` | |  
| [test/test\_generator.cpp](https://app.codecov.io/gh/boostorg/locale/pull/182?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2dlbmVyYXRvci5jcHA=) | `99.52% <100.00%> (ø)` | |  
| [test/test\_winapi\_formatting.cpp](https://app.codecov.io/gh/boostorg/locale/pull/182?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3dpbmFwaV9mb3JtYXR0aW5nLmNwcA==) | `95.50% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/182?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/182?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e910a19...2fe2030](https://app.codecov.io/gh/boostorg/locale/pull/182?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
