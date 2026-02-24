# #126 Make `date_time` movable  [Merged]

> Username: Flamefire  
> Created at: 2022-10-22 15:17:03 UTC  
> Updated at: 2022-10-26 18:06:28 UTC  
> Merged at: 2022-10-26 06:11:04 UTC  
> Closed at: 2022-10-26 06:11:04 UTC  
> Url: https://github.com/boostorg/locale/pull/126  

Improves performance  
Closes #40

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-10-25 01:22:37 UTC  
> Updated_at: 2022-10-25 15:57:24 UTC  
> Url: https://github.com/boostorg/locale/pull/126#issuecomment-1289853830  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/126?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#126](https://codecov.io/gh/boostorg/locale/pull/126?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3c82c4b) into [develop](https://codecov.io/gh/boostorg/locale/commit/cd1fef6739f7ab6e8f9a640cf4ade6fcf2fccdff?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (cd1fef6) will **decrease** coverage by `0.02%`.  
> The diff coverage is `74.84%`.  
  
> :exclamation: Current head 3c82c4b differs from pull request most recent head bb8059d. Consider uploading reports for the commit bb8059d to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/locale/pull/126/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/locale/pull/126?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #126      +/-   ##  
===========================================  
- Coverage    87.77%   87.74%   -0.03%       
===========================================  
  Files          102      102                
  Lines         9016     9084      +68       
===========================================  
+ Hits          7914     7971      +57       
- Misses        1102     1113      +11       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/126?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/boundary/facets.hpp](https://codecov.io/gh/boostorg/locale/pull/126/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvYm91bmRhcnkvZmFjZXRzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/boundary/index.hpp](https://codecov.io/gh/boostorg/locale/pull/126/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvYm91bmRhcnkvaW5kZXguaHBw) | `98.76% <ø> (ø)` | |  
| [include/boost/locale/boundary/segment.hpp](https://codecov.io/gh/boostorg/locale/pull/126/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvYm91bmRhcnkvc2VnbWVudC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/collator.hpp](https://codecov.io/gh/boostorg/locale/pull/126/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvY29sbGF0b3IuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/conversion.hpp](https://codecov.io/gh/boostorg/locale/pull/126/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvY29udmVyc2lvbi5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/encoding.hpp](https://codecov.io/gh/boostorg/locale/pull/126/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZW5jb2RpbmcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/encoding\_errors.hpp](https://codecov.io/gh/boostorg/locale/pull/126/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZW5jb2RpbmdfZXJyb3JzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/encoding\_utf.hpp](https://codecov.io/gh/boostorg/locale/pull/126/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZW5jb2RpbmdfdXRmLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/format.hpp](https://codecov.io/gh/boostorg/locale/pull/126/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZm9ybWF0LmhwcA==) | `94.32% <ø> (+0.04%)` | :arrow_up: |  
| [include/boost/locale/formatting.hpp](https://codecov.io/gh/boostorg/locale/pull/126/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZm9ybWF0dGluZy5ocHA=) | `99.14% <ø> (+<0.01%)` | :arrow_up: |  
| ... and [46 more](https://codecov.io/gh/boostorg/locale/pull/126/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/126?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/126?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [fe8c769...bb8059d](https://codecov.io/gh/boostorg/locale/pull/126?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
