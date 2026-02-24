# #130 Fix use of ICU cache [Merged]

> Username: Flamefire  
> Created at: 2022-10-26 17:58:46 UTC  
> Updated at: 2022-10-27 11:35:34 UTC  
> Merged at: 2022-10-27 11:35:31 UTC  
> Closed at: 2022-10-27 11:35:31 UTC  
> Url: https://github.com/boostorg/locale/pull/130  

The cache created with a locale on a stream was never used.  
Fix that and refactor associated code.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-10-26 20:32:46 UTC  
> Updated_at: 2022-10-27 11:23:40 UTC  
> Url: https://github.com/boostorg/locale/pull/130#issuecomment-1292620931  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/130?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#130](https://codecov.io/gh/boostorg/locale/pull/130?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (65b47cf) into [develop](https://codecov.io/gh/boostorg/locale/commit/bff5950be2c20f9a3c8ac998e6e5712f7c970de0?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (bff5950) will **decrease** coverage by `0.02%`.  
> The diff coverage is `75.84%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/130/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/130?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #130      +/-   ##  
===========================================  
- Coverage    87.84%   87.81%   -0.03%       
===========================================  
  Files          102      103       +1       
  Lines         9074     9036      -38       
===========================================  
- Hits          7971     7935      -36       
+ Misses        1103     1101       -2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/130?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/formatting.hpp](https://codecov.io/gh/boostorg/locale/pull/130/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZm9ybWF0dGluZy5ocHA=) | `99.14% <ø> (ø)` | |  
| [src/boost/locale/icu/formatter.hpp](https://codecov.io/gh/boostorg/locale/pull/130/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZm9ybWF0dGVyLmhwcA==) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/icu/formatter.cpp](https://codecov.io/gh/boostorg/locale/pull/130/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZm9ybWF0dGVyLmNwcA==) | `78.78% <60.90%> (-3.77%)` | :arrow_down: |  
| [src/boost/locale/icu/boundary.cpp](https://codecov.io/gh/boostorg/locale/pull/130/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvYm91bmRhcnkuY3Bw) | `77.45% <100.00%> (ø)` | |  
| [src/boost/locale/icu/formatters\_cache.cpp](https://codecov.io/gh/boostorg/locale/pull/130/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZm9ybWF0dGVyc19jYWNoZS5jcHA=) | `100.00% <100.00%> (ø)` | |  
| [src/boost/locale/icu/formatters\_cache.hpp](https://codecov.io/gh/boostorg/locale/pull/130/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZm9ybWF0dGVyc19jYWNoZS5ocHA=) | `100.00% <100.00%> (+4.83%)` | :arrow_up: |  
| [src/boost/locale/icu/icu\_util.hpp](https://codecov.io/gh/boostorg/locale/pull/130/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvaWN1X3V0aWwuaHBw) | `87.50% <100.00%> (+23.86%)` | :arrow_up: |  
| [src/boost/locale/icu/numeric.cpp](https://codecov.io/gh/boostorg/locale/pull/130/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvbnVtZXJpYy5jcHA=) | `79.66% <100.00%> (ø)` | |  
| [src/boost/locale/icu/uconv.hpp](https://codecov.io/gh/boostorg/locale/pull/130/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvdWNvbnYuaHBw) | `78.12% <100.00%> (ø)` | |  
| [include/boost/locale/hold\_ptr.hpp](https://codecov.io/gh/boostorg/locale/pull/130/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvaG9sZF9wdHIuaHBw) | `95.65% <0.00%> (-4.35%)` | :arrow_down: |  
| ... and [2 more](https://codecov.io/gh/boostorg/locale/pull/130/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/130?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/130?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [bff5950...65b47cf](https://codecov.io/gh/boostorg/locale/pull/130?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
