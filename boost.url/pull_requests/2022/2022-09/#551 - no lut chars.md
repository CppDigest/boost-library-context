# #551 no lut chars [Closed]

> Username: alandefreitas  
> Created at: 2022-09-15 16:38:43 UTC  
> Updated at: 2022-09-19 21:26:44 UTC  
> Closed at: 2022-09-15 19:49:09 UTC  
> Url: https://github.com/boostorg/url/pull/551  

fix #547

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-15 16:59:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/551#pullrequestreview-1109560905  

📁 include/boost/url/impl/url_base.ipp

```diff
2273 |             string_view(it, 3));
2273 |-         if (cs(buf))
2274 |+         if (allowed(buf))
```

> Username: vinniefalco  
> Created_at: 2022-09-15 16:59:33 UTC  
> Url: https://github.com/boostorg/url/pull/551#discussion_r972231453  

this looks better


---

## Comment 2

> Username: vinniefalco  
> Created_at: 2022-09-15 16:59:48 UTC  
> Url: https://github.com/boostorg/url/pull/551#issuecomment-1248370579  

merge this when it is ready

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-09-15 17:02:28 UTC  
> Updated_at: 2022-09-15 17:08:28 UTC  
> Url: https://github.com/boostorg/url/pull/551#issuecomment-1248373068  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/551?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#551](https://codecov.io/gh/boostorg/url/pull/551?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (674b7ea) into [develop](https://codecov.io/gh/boostorg/url/commit/49112f4a0d1705799c17ef8b622ac42f51efdafa?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (49112f4) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/551/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/551?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #551      +/-   ##  
===========================================  
- Coverage    96.70%   96.70%   -0.01%       
===========================================  
  Files          137      137                
  Lines         6618     6617       -1       
===========================================  
- Hits          6400     6399       -1       
  Misses         218      218                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/551?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/url\_base.hpp](https://codecov.io/gh/boostorg/url/pull/551/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX2Jhc2UuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/boostorg/url/pull/551/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `98.72% <100.00%> (-0.01%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/551?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/551?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [49112f4...674b7ea](https://codecov.io/gh/boostorg/url/pull/551?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
