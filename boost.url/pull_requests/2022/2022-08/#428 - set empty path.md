# #428 set empty path [Closed]

> Username: alandefreitas  
> Created at: 2022-08-18 20:34:08 UTC  
> Updated at: 2022-08-30 21:03:47 UTC  
> Closed at: 2022-08-19 20:54:07 UTC  
> Url: https://github.com/boostorg/url/pull/428  

fix #426

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-18 20:39:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/428#pullrequestreview-1077977155  

📁 include/boost/url/impl/url_base.ipp

```diff
1082 |-     // start of output
1083 |-     auto dest = s_ + p0;
1082 |+     if (s_)
```

> Username: vinniefalco  
> Created_at: 2022-08-18 20:39:27 UTC  
> Updated_at: 2022-08-18 20:39:47 UTC  
> Url: https://github.com/boostorg/url/pull/428#discussion_r949579783  

so we were/are calling `reserve` with zero in this case?


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-08-18 20:48:37 UTC  
> Updated_at: 2022-08-19 20:53:01 UTC  
> Url: https://github.com/boostorg/url/pull/428#issuecomment-1219946621  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/428?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#428](https://codecov.io/gh/CPPAlliance/url/pull/428?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (cdf1919) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/d365586c1b906982cdcda43ae2d9f69b46bfb6a3?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (d365586) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head cdf1919 differs from pull request most recent head aded12a. Consider uploading reports for the commit aded12a to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/428/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/428?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #428      +/-   ##  
===========================================  
- Coverage    97.91%   97.91%   -0.01%       
===========================================  
  Files          132      132                
  Lines         6343     6337       -6       
===========================================  
- Hits          6211     6205       -6       
  Misses         132      132                
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/428?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/detail/any\_path\_iter.hpp](https://codecov.io/gh/CPPAlliance/url/pull/428/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2FueV9wYXRoX2l0ZXIuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/CPPAlliance/url/pull/428/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `98.87% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/url/impl/error.ipp](https://codecov.io/gh/CPPAlliance/url/pull/428/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9lcnJvci5pcHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/url/grammar/impl/error.ipp](https://codecov.io/gh/CPPAlliance/url/pull/428/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL2Vycm9yLmlwcA==) | `100.00% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/428?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/428?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [d365586...aded12a](https://codecov.io/gh/CPPAlliance/url/pull/428?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2022-08-19 20:54:07 UTC  
> Url: https://github.com/boostorg/url/pull/428#issuecomment-1221085036  

Merged

---
