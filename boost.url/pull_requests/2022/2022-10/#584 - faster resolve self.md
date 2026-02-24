# #584 faster resolve self [Closed]

> Username: alandefreitas  
> Created at: 2022-10-07 21:08:26 UTC  
> Updated at: 2022-10-13 04:48:03 UTC  
> Closed at: 2022-10-10 19:55:22 UTC  
> Url: https://github.com/boostorg/url/pull/584  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-07 21:18:06 UTC  
> Url: https://github.com/boostorg/url/pull/584#issuecomment-1272094099  

GCOVR code coverage report [https://584.url.prtest.cppalliance.org/gcovr/index.html](https://584.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://584.url.prtest.cppalliance.org/genhtml/index.html](https://584.url.prtest.cppalliance.org/genhtml/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-10-07 21:25:30 UTC  
> Updated_at: 2022-10-07 21:27:53 UTC  
> Url: https://github.com/boostorg/url/pull/584#issuecomment-1272098986  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/584?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#584](https://codecov.io/gh/boostorg/url/pull/584?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (4f7a6df) into [develop](https://codecov.io/gh/boostorg/url/commit/c3ef82e9cf46336c10d8074c1667c5818a46f40f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c3ef82e) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/584/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/584?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #584   +/-   ##  
========================================  
  Coverage    96.81%   96.81%             
========================================  
  Files          139      139             
  Lines         6680     6688    +8       
========================================  
+ Hits          6467     6475    +8       
  Misses         213      213             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/584?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/authority\_view.ipp](https://codecov.io/gh/boostorg/url/pull/584/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9hdXRob3JpdHlfdmlldy5pcHA=) | `79.20% <100.00%> (+0.33%)` | :arrow_up: |  
| [include/boost/url/impl/url\_base.ipp](https://codecov.io/gh/boostorg/url/pull/584/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfYmFzZS5pcHA=) | `99.02% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/url/impl/url\_view\_base.ipp](https://codecov.io/gh/boostorg/url/pull/584/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC91cmxfdmlld19iYXNlLmlwcA==) | `98.60% <100.00%> (+<0.01%)` | :arrow_up: |  
| [include/boost/url/url\_view\_base.hpp](https://codecov.io/gh/boostorg/url/pull/584/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXdfYmFzZS5ocHA=) | `100.00% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/584?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/584?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c3ef82e...4f7a6df](https://codecov.io/gh/boostorg/url/pull/584?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-10-07 21:27:41 UTC  
> Url: https://github.com/boostorg/url/pull/584#issuecomment-1272100311  

seems ok

---
